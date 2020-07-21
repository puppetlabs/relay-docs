# Relay types

Relay's workflow dialect uses YAML "tag" notation, indicated by a `!`, to identify custom data types that Relay associates with code that it should run while it is executing the workflow. This allows workflows to have dynamic values, instead of hard coding everything directly in the YAML. There are several top-level types described here, plus a set of data-manipulation functions accessed as `!Fn.<function>`, which are [documented in the function reference](../reference/relay-functions.md).

## !Connection

Most workflows require some form of authentication. `!Connection` provides a way to add service credentials to your account, which all workflows in your account can access. Workflows that require a `!Connection` in order to run can access it like this:

```yaml
- name: describe-instances
  image: relaysh/ec2-step-describe-instances
  spec:
    aws:
      connection: !Connection { type: aws, name: my-aws-account  }
      region: !Parameter region
```

Then, add the required credentials for the connection (in the example: `my-aws-account`) in the **Settings** sidebar.

Connections can be reused across workflows. Referencing the same `!Connection` by name in another workflow will automatically use the defined connection.

See also the documentation on [Managing connections to your workflow](../using-workflows/managing-connections.md).

## !Secret

Use this to indicate the value is a named secret that's stored by Relay. The value needs to exactly match the secret name. If the secret doesn't exist, the workflow will not run. Unlike connections, secrets are scoped to a single workflow.

The most common usage for `!Secret` is in the `spec` for a given step, to indicate the value needs to be looked up from the secret store:

```yaml
steps:
  - name: use-a-secret
    image: relaysh/core
    spec:
      secretpass: !Secret password
```

See the section on [adding and managing secrets](../using-workflows/managing-secrets.md) for more detail on secrets in Relay.

## !Parameter

Similar to `!Secret`, `!Parameter` is most useful in a step's `spec` to indicate the value needs to be provided by the user or trigger that initiates the workflow. The name of the parameter needs to match one given in the `parameters` section of the workflow; the field you _assign_ it to in the spec, however, can be anything.

```yaml
parameters:
  myparameter:
    description: A parameter the user needs to provide
    default: "A silly default value"
steps:
  - name: use-a-parameter
    image: relaysh/core
    spec:
      userparam: !Parameter myparameter
```

For more information on parameters, see [Passing data into workflow steps](../using-workflows/passing-data-into-workflow-steps.md).

## !Output

Use the `!Output` type to indicate the value is provided by a previous step. `!Output` accepts a mapping with two keys: 

* `from`, whose value is the `name` field of the step from which you're collecting the output 
* `name`, the name of the output whose value to use. 

To _produce_ the output, use the `ni` tool in the originating step.

Using the `!Output` type in a step creates an ordering dependency; Relay will realize it needs to run the step that produces the value before the one that consumes it.

Here's a simple example that runs the `date` program to get the current date and time, then uses that value in the following step:

```yaml
steps:
  - name: make-output
    image: relaysh/core
    input: ni output set --key dynamicdate --value "$(/bin/date)"
  - name: use-output
    image: relaysh/core
    spec:
      mydate: !Output {from: make-output, name: dynamicdate}
```

For more information on outputs, see [Passing data into workflow steps](../using-workflows/passing-data-into-workflow-steps.md).

## !Fn

`!Fn` is short for "function". It is a special data type that indicates to the Relay service that it should run custom code (the function) in order to produce the result. Functions allow you to manipulate data between steps and are also, when used as the value for a `when` keyword, the way Relay implements conditional execution. The general form of using functions is:

```yaml
steps:
  - name: use-a-function
    image: relaysh/core
    spec:
      concatenation: !Fn.concat["a value", "a second value"]
```

See the [Relay function reference](../reference/relay-functions.md) for the full list of functions and their usage.

## !Answer

`!Answer` is an internal type that Relay uses to implement [Approval steps](../using-workflows/adding-an-approval-step.md).

## !Data

`!Data` is a type that's only valid in the context of a [trigger section of a workflow](../reference/relay-workflows.md). It allows you to extract the contents of a field from an incoming event payload for use in your workflow.
