# Relay types

The Relay workflow syntax follows the [YAML version 1.2 specification](https://yaml.org/spec/1.2/spec.html). The examples below list every way you can reference Relay types in a workflow, starting with the most straightforward method.

## Secrets

Use one of the following to reference a secret, where `password` is the name of the secret:

```
!Secret password
!Secret [password]
!Secret {name: password}
{$type: Secret, name: password}
```

For more information on secrets, see [Adding secrets](../using-workflows/adding-secrets).

## Parameters

Use one of the following to reference a parameter, where `message` is the name of the parameter:

```
!Parameter message
!Parameter [message]
!Parameter {name: message}
{$type: Parameter, name: message}
```

For more information on parameters, see [Passing data into workflow steps](../using-workflows/passing-data-into-workflow-steps).

## Outputs

Use one of the following to reference an output. Outputs must include the step name for the step from which you're collecting the output, and the name of the output:

```
!Output [stepName, outputName]
!Output {from: stepName, name: outputName}
{$type: Output, from: stepName, name: outputName}
```

For more information on outputs, see [Passing data into workflow steps](../using-workflows/passing-data-into-workflow-steps).

