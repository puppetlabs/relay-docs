# Passing data into workflow steps

Use parameters and outputs to pass data into your workflow steps.

Use a parameter to pass external data into a workflow step, and an output to pass data from one step into another step in a workflow.

## Passing external data into a step

To pass external data into a workflow step, use the parameter type in your step specification. Before you declare a parameter in your step specification, you must declare it at the top of your workflow. You can add an optional `default` value for the parameter in this section:

```
parameters:
  channel:
    description: Slack channel (include preceding hashtag) 
    default: #nebula-workflows
```

Use the following syntax to declare the parameter in your step specification:

```
channel:
  !Parameter channel
```

This Slack notification example uses parameters to pass a Slack channel name and a custom message into the workflow step. When you run the workflow, Nebula asks you to enter values for `channel` and `message`.

```
version: v1
description: Notify team members with Slack

parameters:
  channel:
    description: Slack channel (include preceding hashtag)
    default: #nebula-workflows
  message:
    description: Slack message

steps:
- name: slack-notify
  image: projectnebula/slack-notification:latest
  spec:
    apitoken:
       !Secret slack-token
    channel:
       !Parameter channel
    message:
       !Parameter message
```

For more information on using a Slack notification step, see 
[Notify your team with Slack](https://github.com/puppetlabs/nebula-workflow-examples/tree/master/example-workflows/notify-slack).

## Passing internal workflow data into a step

If you need to pass data from one step in your workflow to another step, use an output type. Declare the output in the step specification for the step where you are passing in the data. The step in which you declare an output must depend on the step from which you're collecting the output. Some steps do not produce outputs. Check the step documentation to find out if it produces outputs.

Your output declaration must include the `stepName` for the step from which you're collecting the output:

```
message: 
  !Output [make-message, message]
```

The example below uses an output to pass a Slack notification message from the `make-message` step to the `notify` step.

```
apiVersion: v1
description: Passes information from a simple message-maker step to a Slack notification step.

steps:
- name: make-message
  image: projectnebula/message-maker:latest
  spec:
    message: 
      !Secret secret-message
- name: notify
  image: projectnebula/slack-notification:latest
  dependsOn:
  - make-message
  spec:
    apitoken: 
      !Secret slack-token
    channel: "#nebula-workflows"
    message: 
      !Output [make-message, message]
```

