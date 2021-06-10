# Adding an approval step

Add manual approvals to your Relay workflow when you need more control over when something is deployed. For example, you could prevent a deployment to your production environment without an approval from your engineering lead.

After you add an approval to your workflow, an approver can accept or reject it from the workflow run page in the Relay web interface. To respond to an approval request, you must have Approver or Administrator level of access.

Approvals are similar to regular steps, although they do not require an `image` key. Approvals consist of the following keys:

-   `name`: A string. The name of your approval.

-   `description`: A string. A description of your approval.

-   `type`: A string. Use `approval` to define the step as an approval.

-   `dependsOn`: A string, or an array of strings. (Optional) The steps that need to complete successfully before Relay requests the approval.

An approval that does not list a dependency under `dependsOn` can be approved or rejected at any time. An approval that does list dependencies can only be approved once those dependencies have successfully completed.

A step that immediately follows your approval should list the approval step as a dependency using `dependsOn.` Multiple steps can depend on a single approval.

An example of an approval with a `deploy-prod` step that depends on it:

```yaml
steps:
  - name: prod-approval
    description: Deploying to production requires approval
    type: approval
  - name: deploy-prod
    image: relaysh/terraform-step-terraform:latest
    spec:
      vars:
        gcp_region: us-east1
        gcp_location: us-east1-b
        gcp_project: app-prod
      workspace: app
      directory: provision-gke/infra/
      credentials:
        credentials.json: ${secrets.credentials}
      git:
        name: app
        repository: https://github.com/apptime/app
    dependsOn:
    - prod-approval
```

In this workflow, an approver can only take action on the `prod-approval` step after the `provision-k8s` step has completed. After the approver accepts `prod-approval`, Relay executes `deploy-prod`, which depends on `prod-approval`.

Only users with the Approver or Administrator roles can approve an approval step. You can assign roles to users from **Access control** in the Relay web interface. If an approver rejects an approval step, or the approval's timer expires, the workflow run fails. Approval steps currently expire after an hour.