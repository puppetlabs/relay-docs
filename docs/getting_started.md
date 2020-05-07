# Getting Started with Relay

This is a guided tour to walk you through getting started with Relay. It takes about 15 minutes to complete. By the end of it, you'll be able to use the service to automate tasks of your own, replacing digital duct tape with reusable, on-demand workflows.

## Create an account

First things first. You'll need an account on the service to get started, so browse to the [Relay website](https://nebula.puppet.com/) and create an account.

<!--  These steps are TK , don't know what Library UX will be yet
## Browse the workflow library
## Pick the "Getting Started" workflow
## Add it to your account
--> 
## Create a simple workflow

Relay is based around the idea of Workflows, which combine useful activites together to accomplish a specific task. Workflows are written in YAML and stored on the service so they can be triggered manually or via incoming events. Let's start by creating a simple "Hello World" workflow that just executes an "echo" command with any argument you give it.

Click the **Add workflow** button in the top right corner. In the dialog box, give it a name of `hello-world` and a description "Welcome to Relay". You'll get dropped into an editor window, where you can copy-and-paste the following workflow, then click the "Save changes" button on the top right of the editor window.

```yaml
parameters:
  message:
    description: "The message to output from the step"
steps:
- name: hello-world
  image: projectnebula/core
  input:
  - echo "Hello world. Your message was $(ni get -p {.message})"
```

This workflow starts by defining a parameter named `message`, whose value will be filled in as it's executed. Then it defines a step named `hello-world`, which uses a container image stored on Docker hub under [`projectnebula/core`](https://hub.docker.com/r/projectnebula/core), which is a general-purpose Alpine Linux based image curated by Puppet. The container is executed with the value of the `input` step as its entrypoint. The "Hello world" message uses a command-line tool built for Relay called "ni" to inject the value of the message parameter into its output. 

> Further reading: Parameter syntax, container images, ni utility

## Run it via the GUI

Once you save the workflow, use the "Run" button in the top right to execute it. You'll get a dialog box prompting you to fill in the value of the "message" parameter, so enter something witty (if you need a suggestion, try "relay.sh r00lz") and click "Run workflow". 

Your workflow run will be queued on the service and then executed. You'll see some statistics and a graph visualization of the step sequence. With only one step, this workflow's graph just has one node; click it to see details of it in the sidebar. 

Once the step completes, you can select the "Code and data" tab to show the workflow code we just wrote and expand the collapsed right sidebar to show the input for the "message" parameter. 

Select the "Logs" tab to see the earth-shattering results of your workflow run.

> Further reading: More example workflows, Passing data, Step ordering

## Install the CLI
## Download the workflow
## Modify it to do something slightly different
## Upload it back to the service
## Run it via the GUI, supplying parameters
## View results in the GUI
## Modify Workflow to add a secret
## Supply that secret value
## Run it via CLI
## View results via CLI
## Next steps...