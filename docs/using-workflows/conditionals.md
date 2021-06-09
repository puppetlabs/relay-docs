# Conditional step execution

You can use the `when` key to control whether or not Relay executes a step.

If the `when` key is present in a step definition, Relay determines whether the step should execute by evaluating its content. The `when` key is a single boolean expression or an array of boolean expressions that are combined using the `&&` operator. If the key evalutes to `true`, Relay executes the step. Otherwise, the step is skipped.

Note that there is no type coercion applied to `when` values. If the content cannot be evaluated to a boolean, the step will produce an error and your workflow run will fail.

```yaml
steps:
- name: example
  image: alpine
  when: ${parameters.env == 'development'}
```

In this example, Relay only executes the `example` step if the `env` parameter is `development`.

## Writing conditions

Relay's template engine includes a comprehensive set of [boolean](../reference/relay-expressions.md#boolean) and [comparison](../reference/relay-expressions.md#comparison) operators that can be used to produce values for `when` keys.

## Example of a workflow with conditional execution

Consider a scenario where you have two environments, `development` and `production`. You want to deploy a monitoring system only against production. In the following workflow, the `deploy-monitoring` step uses `when` along with the `!=` operator to make sure that Relay only deploys the monitoring system when `env` is not `development`.

Similarly, in this scenario, you want to send a notification to Slack when you deploy to production. The `slack-notify` step uses `when` together with the `==` operator to ensure that Relay only notifies Slack when `env` is `production`.

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
  when: ${parameters.env != 'development'}

- name: slack-notify
  image: relaysh/slack-step-message-send
  spec:
    apitoken: ${secrets.'slack-token'}
    channel: ${parameters.channel}
    message: "production was deployed"
  dependsOn:
  - deploy-monitoring
  when: ${parameters.env == 'production'}
```