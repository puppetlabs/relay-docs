# Managing Connections

Connections allow you to link Relay to other services you use, so that incoming events and outgoing actions can be automated across your whole toolchain.

## Adding connections

To add connections, you must have the Administrator role on your account. Connections are global to the Relay account and are available to any workflows and authorized users on the account. See [Managing Users](../managing-users.md) for more on access control.

Use the "Connections" link in the left navigation bar to show currently configured connections and add new ones. Currently, Relay supports connection types of AWS, Azure, Slack, and SSH keys. If there are additional tools and services you want to provide credentials to, you can use [Secret values](./adding-secrets.md) instead of a provider-specific Connection. These work almost the same but Secrets are specific to a workflow instead of being global. (If you run into this, please [let us know via Github issues](https://github.com/puppetlabs/relay/issues/new/choose) what you'd like to see added!)

![Expand the Setup menu then choose the connection to configure](../images/adding-connections.gif)

## Using Connections

To use a connection in a workflow, set a field's value to `!Connection` and provide a map containing the connection's name and type. For example:

```yaml
steps:
  - name: describe-instances
    image: projectnebula/ec2-describe-instances
    spec:
      aws:
        connection: !Connection { type: aws, name: my-aws-account }
```

If you reference a Connection in a workflow you're viewing or editing on the web app, Relay will check that the connection actually exists and prompt you to create it if not. To set the required values for the connection, on the workflow's page, expand the "Setup" menu on the header bar, then find the connection you specified. Click the ( + ) to add the connection values. Once you create a connection, you cannot view its values again; you can only overwrite or delete them.

When you run a workflow, actions that need a connection request it from Relay's secret store. You can use secrets inside an action with one of the Relay SDKs. This example snippet uses the Python SDK to make use of the connection in the workflow above:

```python
from nebula_sdk import Interface, Dynamic as D

relay = Interface()
sess = boto3.Session(
  aws_access_key_id=relay.get(D.aws.connection.accessKeyID),
  aws_secret_access_key=relay.get(D.aws.connection.secretAccessKey),
  region_name=relay.get(D.aws.region),
)
```
This is a partial snippet; [see the full step code here](https://github.com/relay-integrations/relay-aws-ec2/blob/master/actions/steps/ec2-describe-instances/step.py).

## Implementation details

Secrets and Connections are very similar; see the [implementation section of the Secrets docs](./adding-secrets.md) for more information on how they work and guidance on how to use them.