# Relay workflows

Use a Relay workflow to define the distinct steps in the task you're automating.

Relay workflows use four top-level maps: `description`, `parameters`, `steps`, and `triggers`. Of these, only `steps` is required.

## Description

Start your workflow with the `description` key. For example:

```yaml
description: This workflow prints out a hello message to the logs. It also runs some simple terminal commands and prints their results to the logs.
```

## Parameters

Parameters enable Relay to pass data into the workflow as it runs. You can optionally provide a default value for each parameter, which a user-specified value will override at runtime. If you do not provide a default and do not supply a value for any parameter, the workflow run will fail.

Relay currently only supports directly supplying parameter values as strings. Triggers can pass complex data structures as paramater values; see the [binding section](#binding) for details.

```yaml
parameters:
  myparam:
    description: "A typical user supplied parameter"
    default: defaultvalue
```

## Steps

The `steps` key makes up the body of your workflow. It contains an array of steps, where each step must consist of a `name`, `image`, and `spec`, plus optional `dependsOn` and `env` keys.

### name

The name of the step. Must be unique across the workflow.

**Type:** String

```yaml
name: echo
```

### image

The container image and tag you're using for the step. Relay's default registry is [Docker Hub](https://hub.docker.com), so if the image is hosted there you can use the short `imagename:tag` syntax. Otherwise, use the long form like `gcr.io/account/image:tag`

**Type:** String

```yaml
image: alpine:latest
```

### env

This key allows you to set environment variables which will be available in the step's execution environment. This is useful because many container images are built to look for particular environment variables to adjust their behavior or receive data at runtime.

`env` contains a map of key-value pairs, whose keys are the exact (case-sensitive) variables to set. The values can be fixed strings or use [template expressions](relay-expressions.md) to access parameter, secret, connection, or output data.

**Type**: Map

```yaml
env:
  AWS_ACCESS_KEY_ID: ${parameters.awsAccessKeyID}
  AWS_SECRET_ACCESS_KEY: ${secrets.awsSecretAccessKey}
```
### spec

Short for "specification"; this section provides context that's specific to the step. The contents of `spec` will depend on the implementation of the task container; programmers may find it useful to think of it as providing values to a function. For example, a step which uses the [relaysh/jira-server-step-issue-transition](https://hub.docker.com/r/relaysh/jira-server-step-issue-transition) container to close a Jira ticket requires `url` and `issue` keys in its `spec`. For a list of step containers curated by Puppet see the [relay-integrations org on GitHub](https://github.com/relay-integrations).

**Type:** Map

### dependsOn

Indicates that this step depends on another step in the workflow. Each value must be a valid `name` attribute for another step. This key is useful if you need to set an explicit sequential order for your steps. Without `dependsOn` or implicit ordering requirements (see the [outputs data key](relay-expressions.md#outputs)), Relay will run your steps in parallel to speed up execution.

**Type:** String or array of strings

```yaml
dependsOn:
- deploy_test_cluster
```

### input

If the container is configured to accept it, you can use the `input` map to provide a list of commands that Relay passes to the entrypoint.

**Type:** Array of strings

### inputFile

If the container is configured to accept it, you can use the `inputFile` attribute to supply a URL to a file which Relay will download and provide as input to the entrypoint. The URL must be accessible to the public internet.

**Type:** String

## Triggers

Triggers map incoming events to workflows. The `triggers` key contains an array of individual triggers, any of which can cause the workflow to run. Each trigger must have `name` and `source` keys, and may optionally have `binding` and `when` keys.

There is a separate guide to [adding triggers to your workflows](../using-workflows/using-triggers.md) with an end-to-end description of Relay's trigger system.

### name

A name for this trigger; must be unique to the workflow.

**Type:** String

### source

The source for the trigger is a description of the event which will cause the workflow to run. There are currently three kinds of sources available: `schedule`, `webhook`, and `push`. The `source` map requires a `type` attribute to indicate which one to use and each type has additional required fields.

**Type:** Map

#### schedule

A schedule trigger works on a time-based schedule. It needs a key named `schedule` whose value is a string in the standard Unix cron syntax. There's a handy [crontab syntax generator](https://crontab.guru/) to help build a schedule if you're not familiar with cron. To determine whether Relay supports a particular advanced cron feature, refer to the docs for the underlying implementation at [github.com/robfig/cron](https://pkg.go.dev/github.com/robfig/cron/v3?tab=doc).

```yaml
triggers:
 - name: my-schedule-trigger
   source:
     type: schedule
     schedule: '*/5 * * * *'   # runs every 5 minutes
```

#### webhook

A webhook trigger processes an incoming webhook payload from an external service. Relay runs a container image in response to the webhook payload, so this trigger requires an `image` field that is a reference to a container image hosted on a registry, similar to the `image` field in a `step` definition.

```yaml
triggers:
  - name: my-webhook-trigger
    source:
      type: webhook
      image: relaysh/dockerhub-trigger-image-pushed
```

This example runs the `dockerhub-trigger-image-pushed` container in response to receiving a webhook. Once you add a webhook trigger to a workflow, the Relay app will prompt you to complete the webhook configuration in the sidebar. For information on building your own webhook containers, see the [Integrating with Relay](../developers/integrating-with-relay.md) documentation.

Webhook triggers require a [binding](#binding) to map outputs from the trigger container to workflow parameters.

#### push

A push trigger uses events that come into Relay through its API to trigger workflows. Unlike webhook triggers, push triggers do not require a container image because they are handled by the Relay service itself. If you have an external system which can generate a JSON payload but doesn't support webhooks, push triggers are a good option.

To use push triggers, add its definition and `binding` to your workflow, then view it on the Relay web app to see your trigger-specific authentication token. The external system should make a POST request with an `Authorization` header containing the token to `https://api.relay.sh/api/events`. The body of the request must be a valid JSON payload in a top-level key called `data`. The workflow then uses the `event` input data key in a [template expression](relay-expressions.md) to extract values from the payload and bind them to workflow parameters.

For example, a workflow containing a push trigger would look like this:

```yaml
triggers:
  - name: my-push-trigger
    source:
      type: push
    binding:
      parameters:
        message: ${event.messageFromPush}
parameters:
  message:
    description: A message to show
steps:
  - name: display-message
    image: relaysh/core
    spec:
      message: ${parameters.message}
    input:
      - echo "My message was $(ni get {.message})"
```

This workflow will be triggered by an HTTP request such as this curl command:

```shell
export TOKEN=... # get this from the web app
curl -X POST -H "Authorization: Bearer $TOKEN" \
   -d '{"data": {"messageFromPush": "This is a push event"}}' \
   https://api.relay.sh/api/events
```

### binding

The `binding` key of a trigger definition maps incoming data from an event to parameters in a workflow. This allows you to extract specific fields from the json payload of a webhook or push event. A `binding` has one field, `parameters`, which is a map whose keys must match the names of parameters inside the workflow. The values can use [template expressions](relay-expressions.md), and in particular the `event` data key, to extract and manipulate data from the event into the form the workflow parameter expects.

```yaml
triggers:
  - name: my-webhook-trigger
    source:
      type: webhook
      image: relaysh/dockerhub-trigger-image-pushed
    binding:
      parameters:
        dockerTagName: ${event.tag}
parameters:
  dockerTagName:
    default: latest
```

Expanding on the Docker Hub example, this trigger extracts the `tag` field from the incoming webhook and maps it to the parameter `dockerTagName`, which is used in the workflow to override the default value of `latest`.

### when

The same syntax that Relay uses to provide conditional execution of steps is also available for triggers. This uses the `when` key, whose value is an expression that must evaluate to "true" in order for the workflow to run. See the [Conditional execution](../using-workflows/conditionals.md) docs for more details on the syntax and usage.

Similar to the `binding` map, it's handy to make decisions about execution based on the contents of the incoming event. You can use the `event` data in the `when` key for a trigger.

```yaml
triggers:
  - name: conditional-trigger
    source:
      type: webhook
      image: relaysh/core
    when: ${event.environment == 'production'}
```
