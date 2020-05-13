# Adding secrets

Add a secret to your workflow and then use Relay's web interface to store the content of the secret securely on the service.

To add a secret to your workflow, set a field's value to the `!Secret` type and provide the secret's name. For example:

```yaml
steps:
  - name: use-a-secret
    image: projectnebula/core
    spec:
      credentials: !Secret credentials
```

To set a value for the secret, on the workflow's page, expand the "Setup" menu on the header bar, then click "New Secret". Make sure the name you provide matches the name of the field in the workflow! Once you create a secret, you cannot view its value again. You can only overwrite or delete it.

**Important:** The value you set for a secret must be a string. If you have multiple key-value pairs to pass in to the secret, or your secret is the contents of a file, you must encode the values using base64 encoding, and use the encoded string as the secret value.

![Expand the Setup menu then choose "New Secret"](../images/new-secret.gif)

Here's an example of a workflow that provisions a Kubernetes cluster on Google Cloud Project \(GCP\). The workflow makes use of two secrets, `cluster-service-account-credentials` and `cluster-project`.

```yaml
version: v1
description: Relay Kubernetes cluster provisioner

steps:
- name: create-k8s-cluster
  image: gcr.io/nebula-tasks/nebula-k8s-provisioner:latest
  spec:
    provider: gcp
    credentials:
      !Secret cluster-service-account-credentials
    project:
      !Secret cluster-project
    clusterName: test-cluster
    stateStoreName: demo-gcp-cluster-state
    nodeCount: 3
    zones: ["us-west1-a"]
```

