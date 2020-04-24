# Adding an approval step to a workflow

Add manual approval steps to your Relay workflow when you need more control over when something is deployed. For example, you could prevent a deployment to your production environment without an approval from your engineering lead.

After you add an approval step to your workflow, an approver can accept or reject the step from the workflow run page in the Relay web interface.

Approval steps are similar to regular task steps, although they do not require an `image` key. Approval steps consist of the following keys:

-   `name`: A string. The name of your approval step.

-   `description`: A string. A description of your approval step.

-   `type`: A string. Use `approval` to define the step as an approval step.

-   `dependsOn`: A string, or an array of strings. (Optional) The step or steps that need to complete successfully before you can take action on the approval step.


An approval step that does not list a dependency under `dependsOn` can be approved or rejected at any time. An approval step that does list dependencies can only be approved once those dependencies have successfully completed.

A step that immediately follows your approval step, and relies on it for approval, should list the approval step as a dependency using `dependsOn.` Multiple steps can depend on an approval step.

An example of an approval step with a `deploy-prod` step that depends on it:

```
…

- name: prod-approval
  description: Deploying to production requires approval 
  type: approval
  dependsOn: provision-k8s
- name: deploy-prod 
  image: projectnebula/terraform:latest
  spec:
    vars:
      gcp_region: us-east1
      gcp_location: us-east1-b
      gcp_project: app-prod
    workspace: app
    directory: provision-gke/infra/
    credentials:
      credentials.json: !Secret credentials
    git:
      name: app
      repository: https://github.com/apptime/app
  dependsOn:
  - prod-approval

```

In this workflow, an approver can only take action on the `prod-approval` step after the `provision-k8s` step has completed. After the approver accepts `prod-approval`, Relay executes `deploy-prod`, which depends on `prod-approval`.

Only users with the approver or administrator roles can approve an approval step. You can assign roles to users from **Access control** in the Relay web interface. If an approver rejects an approval step, or the approval's timer expires, the workflow run fails.

**Note**: Approval steps currently expire after an hour.

