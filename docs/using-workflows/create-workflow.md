# Writing a workflow

Use a Project Nebula workflow to define the steps in your deployment.

Nebula workflows use three top-level keys: `version`, `description`, and `steps`.

Start your workflow with the `version` and `description` keys:

-   `version`: A string. Your workflow version.
-   `description`: A string. A short description of your workflow.

For example:

```
version: v1
description: This workflow prints out a hello message to the logs. It also runs some simple terminal commands and prints their results to the logs.
```

The `steps` key makes up the body of your workflow. Each step consists of a `name`, `image`, and `spec` key, and an optional `dependsOn` key:

-   `name`: A string. The name of the step.

    ```
    name: echo
    ```

-   `image`: A string. The container image and tag you're using for the step.

    ```
    image: alpine:latest
    ```

-   `spec`: Use the specification section to define the task that the step is performing. The contents of `spec` depends on the task's context. For example, a step that uses the Jira resolve step container to close a Jira ticket must include a `url` and `issue` keys. For a list of step containers curated by Puppet see [step containers](../step-specifications.md).
-   `dependsOn`: \(Optional\) A list of strings. Use if the step depends on another step in the workflow. This key is useful if you need to set an explicit sequential order for your steps. If you leave out `dependsOn`, Nebula executes all of the steps in your workflow simultaneously.

    ```
    dependsOn:
    - deploy_test_cluster
    ```


This example workflow deploys three steps. All three steps deploy Alpine containers. The first step prints a "Hello world" message to the workflow's logs. The second and third steps run `ls` and `ps` commands.

```
version: v1
description: Workflow to echo a nice hello world message to the logs

steps:
- name: echo
  image: alpine:latest
  input:
  - echo "Hello world. I am $(whoami)"
  - cat /nebula/spec.json
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

**Note:** The `input` and `command` keys still function, but are deprecated. We're working on updating this page with a better example.

Once you've created your workflow, add it to your Nebula account using the web interface or the CLI.

