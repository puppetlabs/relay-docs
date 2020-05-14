# Adding connections

Connections allow you to link Relay to other services you use, so that incoming events and outgoing actions can be automated across your whole toolchain. 

To use a connection, first add `!Connection` custom type to your workflow, then use Relay's web interface to store the values for the connection securely on the service.

In your workflow, set a field's value to `!Connection` and provide a map containing the connection's name and type. For example:

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

Connections are global to the Relay account. If a connection is defined, any workflows that are created can use the connection by referencing it.

## Managing connections
Connections can be managed from the Connections menu option on the left navigational bar. You can add new connections, override the values for existing connections, or delete existing connections.

