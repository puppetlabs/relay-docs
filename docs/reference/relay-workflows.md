# Workflow reference

Use a Relay workflow to define the distinct steps in the task you're automating.

Relay workflows use four top-level maps: `description`, `parameters`, `steps`, and `triggers`. Of these, only `steps` is required.

## Description

Start your workflow with the `description` key. For example:

```yaml
description: This workflow prints out a hello message to the logs. It also runs some simple terminal commands and prints their results to the logs.
```

## Parameters

Parameters enable Relay to provide outside data to into the workflow as it runs. You can optionally provide a default value for each parameter that can be overriden at runtime. If you do not provide a default and do not supply a value, the workflow run will fail.

Relay currently only supports parameters whose values are strings.

```yaml
parameters:
  myparam:
    description: "A typical user supplied parameter"
    default: defaultvalue
```

## Steps

The `steps` key makes up the body of your workflow. It contains an array of steps, where each step consists of a `name`, `image`, and `spec` key, and an optional `dependsOn` key:

-   `name`: (string) The name of the step. Must be unique across the workflow.

    ```yaml
    name: echo
    ```

-   `image`: (string) The container image and tag you're using for the step. Relay's default registry is [Docker hub](https://hub.docker.com), so if the image is hosted there you can use the short `imagename:tag` syntax. Otherwise, use the long form like `gcr.io/account/image:tag`

    ```yaml
    image: alpine:latest
    ```

-   `spec`: (array) Short for 'specification'; this section provides context that's specific to the step. The contents of `spec` will depend on the implementation of the task container (programmers, think of it as providing values to a function). For example, using the [projectnebula/jira-resolve](https://hub.docker.com/r/projectnebula/jira-resolve) container to close a Jira ticket requires `url` and `issue` keys. For a list of step containers curated by Puppet see [step containers](../step-specifications.md).

-   `dependsOn`: \(string or array of strings\) As the names suggests, this  step depends on another step in the workflow. This key is useful if you need to set an explicit sequential order for your steps. Without `dependsOn` or implicit ordering requirements (see the [!Output type](reference/relay-types.md)), Relay will run your steps in parallel to speed up execution.

    ```yaml
    dependsOn:
    - deploy_test_cluster
    ```


```
version: v1
description: Workflow to echo a nice hello world message to the logs

steps:
- name: echo
  image: alpine:latest
  input:
  - echo "Hello world. I am $(whoami)"
  - cat /relay/spec.json
  spec:
    hello: world
- name: ls
  image: alpine:latest
  command: ls
  args:
  - "-la"
  - "/"
  dependsOn:
    - echo
- name: ps
  image: alpine:latest
  command: ps
  args:
  - "-a"
  dependsOn:
    - echo
```


