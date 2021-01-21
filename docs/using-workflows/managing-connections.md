# Managing connections

Connections allow you to link Relay to other services you use, so that incoming events and outgoing actions can be automated across your whole toolchain.

## Adding connections

To add connections, you must have the administrator role on your account. Connections are global to the Relay account and are available to any workflows and authorized users on the account. See [Managing Users](../managing-users.md) for more on access control.

Use the **Connections** link in the left navigation bar to show currently configured connections and add new ones.  Relay supports connections to many external services and tools including Kubernetes clusters, cloud providers, and apps like Jira and Slack. If you want to connect to a service for which Relay does not have a connection type, you can use [secret values](../using-workflows/managing-secrets.md) instead of a provider-specific connection. They use the same security backend, but secrets are specific to a workflow instead of being global. If you'd like us to support an addiitonal service with a new connection type, please [let us know via Github issues](https://github.com/puppetlabs/relay/issues/new/choose)!

![Manage current connections and add new ones](../images/managing-connections-setup.gif)

## Using connections

To use a connection in a workflow, set a field's value to `!Connection` and provide a map containing the connection's name and type. For example:

```yaml
steps:
  - name: describe-instances
    image: relaysh/ec2-step-describe-instances
    spec:
      aws:
        connection: !Connection { type: aws, name: my-aws-account }
```

If a workflow you're viewing or editing on the web app uses a connection, Relay will check that the connection actually exists and prompt you to create it if not. If you have a connection of the correct type, but your name for it doesn't match the `name` tag in the workflow, you can edit the workflow to adjust the name it's looking for.

If you don't have a connection in your account
1. On the workflow's page, go to the  **Settings** screen.
2. Find the connection the workflow specifies and click **+** to add the connection values. 

Once you create a connection, you cannot view its values again; you can only overwrite or delete them.

When you run a workflow, steps that need a connection request it from Relay's secret store. You can use secrets inside a step with one of the Relay SDKs. This example snippet uses the Python SDK to make use of the connection in the workflow above:

```python
from relay_sdk import Interface, Dynamic as D

relay = Interface()
sess = boto3.Session(
  aws_access_key_id=relay.get(D.aws.connection.accessKeyID),
  aws_secret_access_key=relay.get(D.aws.connection.secretAccessKey),
  region_name=relay.get(D.aws.region),
)
```
This is a partial snippet; [see the full step code here](https://github.com/relay-integrations/relay-aws-ec2/tree/master/steps/aws-ec2-step-instances-describe).

## Implementation details

Secrets and connections are very similar; see the [implementation section of the secrets docs](../using-workflows/managing-secrets.md) for more information on how they work and guidance on how to use them.
