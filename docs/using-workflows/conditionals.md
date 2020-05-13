# Conditional step execution

You can use the `when` keyword, along with the `equals` and `notEquals` functions to control whether or not Relay executes a step.

If `when` is present, Relay executes the step once the conditional data defined under `when` is true. The `when` key is an array of expressions that must output either boolean `true` or boolean `false`. If all booleans in the array are `true`, the condition is considered successful and Relay executes the step. If any of the values are `false`, the condition fails and the step does not continue.

If the `when` key outputs a non-boolean data type, the step returns an error. In practice, the `when` keyword is only effective when combined with the `equals` and `notEquals` functions. Here is an example of a step that contains a `when` block that uses the `equals` function:

```yaml
- name: example
  image: alpine:latest
  when:
  - !Fn.equals [!Parameter env, development]
```

In this example, Relay only executes the `example` step if the `env` parameter is `development`.

## Using conditional functions

The `equals` and `notEquals` functions provide a way to compare data sources:

- `!Fn.equals` returns `true` if the underlying values and types of the arguments are equal.
- `!Fn.notEquals` returns `true` if the underlying values and types of the arguments are not equal.

> **Note:** The string `"true"` does not equal the boolean `true`.

Conditional functions must receive two arguments. If the number of arguments is more or less than 2, the step returns an `ArityError` and the step fails.

Here are all the supported expressions and types you can pass into `equals` and `notEquals`:

- a parameter: `!Parameter example-parameter`
- an output: `!Output stepName outputName`
- a string: `"Just a normal string."`
- a boolean: `true` or `false`
- an integer: `10`, `100`, `35`
- a float: `10.5`, `1.0`, `54.99999`
- an array: `["apple", "banana", "orange", "peach"]`
- a map: `{"vegetable": "carrot", "fruit": "apple"}`

> **Note:** You can't pass `!Secret` into a conditional function.

## Example of a workflow with conditional execution

Consider a scenario where you have two environments, `development` and `production`. You want to deploy a monitoring system only against production. In the following workflow, the `deploy-monitoring` step uses `when` along with the `!Fn.notEquals` condition to make sure that Relay only deploys the monitoring system when `env` is not `development`.

Similarly, in this scenario, you want to send a notification to Slack when you deploy to production. The `slack-notify` step uses `when` together with `!Fn.equals` to ensure that Relay only notifies Slack when `env` is `production`.

```yaml
description: Deploy a thing to environments then conditionally notify slack

parameters:
  channel:
    description: Slack channel (include preceding hashtag)
  env:
    description: the environment to deploy to
    default: development

steps:
- name: deploy-monitoring
  image: alpine:latest
  input:
  - echo "Deploying monitoring to system"
  when:
  - !Fn.notEquals [!Parameter env, development]

- name: slack-notify
  image: projectnebula/slack-notification
  spec:
    apitoken: !Secret slack-token
    channel: !Parameter channel
    message: "production was deployed"
  dependsOn:
  - deploy-monitoring
  when:
  - !Fn.equals [!Parameter env, production]
```