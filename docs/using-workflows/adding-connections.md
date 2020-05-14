# Adding connections

- To add connections, user must be an Administrator role.

Add a connection to your workflow and then use Relay's web interface to store the values of the connection securely on the service.

To add a connection to your workflow, set a field's value to the `!Connection` type and provide the connection's name and type. For example:

```yaml
steps:
  - name: describe-instances
    image: projectnebula/ec2-describe-instances
    spec:
      aws:
        connection: !Connection { type: aws, name: my-aws-account }
```

To set the required values for the connection, on the workflow's page, expand the "Setup" menu on the header bar, then find the connection you specified (in the above example, `my-aws-account`). Click the ( + ) to add the connection values. Once you create a connection, you cannot view its values again. You can only overwrite or delete it.  

![Expand the Setup menu then choose the connection to configure](../images/adding-connections.gif)

**NOTE:** Connections are global to the Relay account. If a connection is defined, any workflows that are created can use the connection by referencing it. 

## Managing connections 
Connections can be managed from the Connections menu option on the left nav. You can add new connections, override the values for existing connections, or delete existing connections. 

## About Connections
Connections are encrypted when entered and remain encrypted until they are used by a workflow.

When workflows are run, workflow actions that require a connection request the connection value from the Relay Interace. Secrets can be accessed from within an action using one of the Relay SDKs:

```
relay = Interface()
sess = boto3.Session(
  aws_access_key_id=relay.get(D.aws.connection.accessKeyID),
  aws_secret_access_key=relay.get(D.aws.connection.secretAccessKey),
  region_name=relay.get(D.aws.region),
)
```
[Example AWS action](https://github.com/relay-integrations/aws-ec2/blob/master/actions/steps/ec2-describe-instances/step.py)

Connections are global to an account and can be referenced by other workflows. 

As any action or workflow can request a connection, some best practices should be followed: 
- When creating credentials, grant the minimum possible credentials for the workflow to run. Workflow connection requirements should be documented. 
- Use service accounts over personal credentials where possible.
- Understand the code for every action running in the workflow. Relay action source code can be found within the [Relay Integrations organization](https://github.com/relay-integrations).