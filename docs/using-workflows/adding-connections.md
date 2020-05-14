# Adding connections

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

