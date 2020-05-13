# Relay types

Relay's workflow dialect uses YAML "tag" notation, indicated by a `!`, to identify custom data types that the Relay service associates with code that it should run the workflow is executing. This allows workflows to have dynamic values, instead of hard coding everything directly in the YAML.  There are several top-level types described here, plus a set of data-manipulation functions accessed as `!Fn.<function>` which are [documented in the function reference](relay-functions.md).

## !Secret

Use this to indicate the value is a named secret that's stored on the service. The value needs to exactly match the secret name. If the secret doesn't exist, the workflow will not run.

The most common usage for `!Secret` is in the `spec` for a given step, to indicate the value needs to be looked up from the secret store:

```yaml
steps:
  - name: use-a-secret
    image: projectnebula/core
    spec:
      secretpass: !Secret password
```

See the section on [adding and managing secrets](../using-workflows/adding-secrets.md) for more detail on secrets in Relay. 

## !Parameter

Similar to `!Secret`, `!Parameter` is most useful in a step's `spec` to indicate the value needs to be provided by the user or trigger that initiates the workflow. The name of the parameter needs to match one given in the `parameters` section of the workflow; the field you _assign_ it to in the spec, however, can be anything.

```yaml
parameters:
  myparameter:
    description: A parameter the user needs to provide
    default: "A silly default value"
steps:
  - name: use-a-parameter
    image: projectnebula/core
    spec:
      userparam: !Parameter myparameter
```

For more information on parameters, see [Passing data into workflow steps](../using-workflows/passing-data-into-workflow-steps.md).

## !Output

Use the `!Output` type to indicate the value is provided by a previous step. Outputs must include the step name for the step from which you're collecting the output, and the name of the output. To _produce_ the output, use the `ni` tool in the originating step. 

Using the `!Output` type in a step creates an ordering dependency; Relay will realize it needs to run the step that produces the value before the one that consumes it.

Here's a simple example that runs the `date` program to get the current date and time, then uses that value in the following step:

```yaml
steps:
  - name: make-output
    image: projectnebula/core
    input: ni output set --key dynamicdate --value "$(/bin/date)"
  - name: use-output
    image: projectnebula/core
    spec:
      mydate: !Output[make-output, dynamicdate]
```

For more information on outputs, see [Passing data into workflow steps](../using-workflows/passing-data-into-workflow-steps.md). 

## !Fn

`!Fn` is short for "function". It is a special data type that indicates to the Relay service that it should run custom code (the function) with in order to produce the result. Functions allow you to manipulate data between steps and are also, when used as the value for a `when` keyword, the way Relay implements conditional execution. The general form of using functions is:

```yaml
steps:
  - name: use-a-function
    image: projectnebula/core
    spec:
      concatenation: !Fn.concat["a value", "a second value"] 
```

See the [Relay function reference](relay-functions.md) for the full list of functions and their usage.

## !Answer

`!Answer` is an internal type that Relay uses to implement [Approval steps](../adding-an-approval-step.md).

## !Data

`!Data` is an internal type that Relay uses to implement Triggers.