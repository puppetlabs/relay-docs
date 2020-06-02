# Integrating with Relay

While there's a growing [ecosystem of integrations](https://relay.sh/integrations/) that connect Relay with other tools and services, it's a big world out there and there are certainly useful integrations that don't yet exist, but should. This guide will help you build an integration between Relay and an external service, so you can automate away your pain.

## Decide on a goal

This may seem self-evident but it's important to determine what your goal is _before_ you begin coding. Relay has several useful points of extensibility, and understanding the purpose of each will help you find a starting point. Integrations consist of containers that are used in different parts of Relay; the umbrella term is **Actions**, and they're further specialized into **triggers**, **steps**, or **queries**.

* **Steps** - Relay runs steps, passing in parameters and secrets, as part of an automation workflow. Most of the work in Relay is done by Steps, which use an SDK to retrieve and modify a workflow's state as it executes.
* **Triggers** - External systems send events to Relay, which handles them by executing a Trigger. The code inside the Trigger determines how to respond to the event. Consider creating a Trigger if you have an event-generating system that either uses webhooks or can craft and send messages with a JSON payload into Relay's API.
* **Queries** - Sometimes you'll need to break out of automated workflow to wait for external input. Queries are a special kind of step which enable Relay to pause a workflow's execution until something outside the system happens, for example when an authorized user responds to an approval request.

All of these actions come together in Workflows which use them to accomplish the task you're trying to do. Workflow authoring is covered in the [Using workflows](./using-workflows.md) documentation, so here we'll focus on creating and using Actions. To further narrow our scope, Queries are currently limited to [manual approvals](./adding-an-approval-step.md) but we're actively working to make them more user-friendly. Please [file a GitHub issue](https://github.com/puppetlabs/relay/issues/new) if you need this feature today!

## Creating Actions

In its simplest form, a Relay action is a container image which runs, does some work, and then exits. It's possible to use any OCI-compliant container, as long as its entrypoint terminates with a `0` exit code upon success and a non-zero code upon failure. However, beyond "hello world" examples you'll likely want an image with more capabilities than just using `alpine:latest`. There are two possible paths here, depending on your use case.

1. If the work you're trying to do can be written in a shell or Python script, use the `relaysh/core` image. This is an Ubuntu-based image that the Relay team maintains, which comes pre-loaded with the Relay SDK. There are `relaysh/core:latest` and `relaysh/core:latest-python` flavors available. You can use these either as a base image in your own Dockerfile or in workflows directly, by specifying an `inputFile` tag whose value is a URL to your script.
2. If there's an image that's maintained by the target of your integration, you can use that as your base image and add the Relay tools in at build time. This will allow you to use the metadata service via either the command-line interface or code SDK.

### Using relaysh/core

Using the `relaysh/core` image is the easiest option because it avoids having to build and push a custom container image, but it restricts you to providing your script in a single file. To use it, make your script accessible via https (github repositories or gists both work fine) and provide the URL to it in the `inputFile` attribute on a `step` or `trigger` definition. There are two variants of the container to specify in the `image` attribute: `relaysh/core:latest`, which accepts a Bash shell as input and `relaysh/core:latest-python` which (predictably) expects a Python script. 

#### relaysh/core Shell Example

The core image isn't suitable for trigger actions, only steps.   (TODO - is that true?)

```yaml
steps:
  - name: run-my-script
    image: relaysh/core:latest
    inputFile: https://git.io/JfiwE
```

#### relaysh/core Python Example

The Python image is usable for both steps and triggers. The following workflow receives a pushed tag from dockerhub and sends a notification with the name of the tag.

```yaml
parameters:
  - name: dockerTagName
    description: The tag of the container which was pushed
triggers:
  - name: my-python-trigger
    source:
      type: webhook
      image: relaysh/core:latest
      inputFile: https://git.io/JfiwD
    binding:
      parameters:
        dockerTagName: !Data tag
steps:
  - name: my-python-notification
    image: relaysh/core:latest-python
    inputFile: TODO
    spec:
      tag: !Parameter dockerTagName
```

### Using an upstream image

While it's possible to use an upstream image without modification, customizing it can greatly increase its usefulness. Adding the Relay SDK or `ni` command-line utility to the container enables access to the Relay service APIs, which allow you to retrieve and set parameters, access secrets, and use Relay's persistent logging framework. Additionally, writing a custom entrypoint allows you to control the container's behavior to ensure it runs correctly in Relay. 

This section assumes you're familiar with the Dockerfiles and the container build/push process.

#### Adding the CLI

The [`ni` CLI utility](./cli/ni.md) is a small Go program meant to be run inside Relay containers. The most common use for it is to get and set variables from the Relay service API from entrypoint shell scripts. To add it to your container, use a snippet like this in your Dockerfile:

```shell
RUN set -eux ; \
    mkdir -p /tmp/ni && \
    cd /tmp/ni && \
    wget https://packages.nebula.puppet.net/sdk/ni/v1/ni-v1-linux-amd64.tar.xz && \
    wget https://packages.nebula.puppet.net/sdk/ni/v1/ni-v1-linux-amd64.tar.xz.sha256 && \
    echo "$( cat ni-v1-linux-amd64.tar.xz.sha256 )  ni-v1-linux-amd64.tar.xz" | sha256sum -c - && \
    tar -xvJf ni-v1-linux-amd64.tar.xz && \
    mv ni-v1*-linux-amd64 /usr/local/bin/ni && \
    cd - && \
    rm -fr /tmp/ni
```

This will download, verify, and install the latest version of the CLI. To use it in your entrypoint, the `ni get` subcommand will (with no arguments) print a JSON string containing all the parameters listed in the step's `spec` section. Given a parameter to retrive, it'll print that in string form (without an enclosing json object).

The `ni output set` subcommand will write values back to the metadata service for use by later steps, and `ni log` will write log messages which can be helpful when debugging.

```yaml
parameters:
  message:
    description: "The message to output from the final step"
steps:
- name: generated-output
  image: relaysh/core
  spec:
    dynamic: "A default value, overridden dynamically"
  input:
  - ni output set --key dynamic --value "$(/bin/date)"
- name: hello-world
  image: relaysh/core
  spec:
    message: !Parameter message
    dynamic: !Output [generated-output,dynamic]
    supersecret: !Secret mysecret
  input:
  - echo "Hello world. Your message was $(ni get -p {.message}), and the generated output was $(ni get -p {.dynamic})."
  - echo "Everything I know about your run is contained in $(ni get)"
  - ni log info "This worked out wonderfully."
```

#### Adding the SDK

If you're using Python in your container, the latest version of the Relay SDK can be installed via pip in your Dockerfile:

```shell
RUN apk --no-cache add bash ca-certificates curl git jq openssh && update-ca-certificates
RUN pip --no-cache-dir install "https://packages.nebula.puppet.net/sdk/support/python/v1/nebula_sdk-1-py3-none-any.whl"
```

The SDK itself does not yet have friendly documentation; the source code is at [puppetlabs/nebula-sdk](https://github.com/puppetlabs/nebula-sdk/tree/master/support/python/src/nebula_sdk) for the brave of heart.


