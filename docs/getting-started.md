# Getting Started with Relay

This is a guided tour to walk you through getting started with Relay. It takes about 15 minutes to complete. By the end of it, you'll be able to use the service to automate tasks of your own, replacing digital duct tape with reusable, on-demand workflows.

## Create an account

First things first. You'll need an account on the service to get started, so browse to the [Relay website](https://nebula.puppet.com/) and create an account. 

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
  spec:
    message: !Parameter message
  input:
  - echo "Hello world. Your message was $(ni get -p {.message})"
```

This workflow starts by defining a parameter named `message`, whose value you'll need to supply before the workflow starts. Then it defines a step named `hello-world`, using a container image stored on Docker hub at [`projectnebula/core`](https://hub.docker.com/r/projectnebula/core), which is a general-purpose Alpine Linux based image curated by Puppet. 

The `spec` map defines keys and their values that will be available inside the step. In this case, we're looking up the value of the global `message` parameter and making it available to the container. Other options besides the `!Parameter` include `!Secret` for accessing encrypted values and a number of utility functions like `!Merge` and `!Concat`. 

The container is executed with the value of the `input` step as its entrypoint. The "Hello world" message uses a command-line tool built for Relay called `ni` to inject the value of the message parameter into its output. 

> Further reading: [Function reference](reference/relay-function.md), [container images](https://github.com/puppetlabs/nebula-steps)

## Run it via the GUI

Once you save the workflow, use the "Run" button in the top right to execute it. You'll get a dialog box prompting you to fill in the value of the "message" parameter, so enter something witty (if you need a suggestion, try "relay.sh r00lz"), then click "Run workflow". 

Your workflow run will be queued on the service and then executed. You'll see some statistics and a graph visualization of the step sequence. With only one step, this workflow's graph just has one node represented as a box; click it to see the step's details in the sidebar. 

Once the step completes, you can select the "Code and data" tab to show the workflow code we just wrote and expand the collapsed right sidebar to show the input for the "message" parameter. 

Select the "Logs" tab to see the earth-shattering results of your workflow run.

> Further reading: [Example workflows](https://github.com/puppetlabs/nebula-workflow-examples), [Passing data into workflows](using-workflows/passing-data-into-workflow-steps.md)

## Install the CLI

The web app is great for visualization, log history, and troubleshooting. For workflow editing and authoring, however, you'll almost certainly want to use the command-line interface, `relay`. 

You can grab the latest release from the [puppetlabs/relay github site](https://github.com/puppetlabs/relay/releases); it's a single binary download so download the latest release for your operating system, install it into your $PATH, and make it executable.

For easy installation and updating on a Mac, you can use [Homebrew](https://brew.sh):

```shell
brew install puppetlabs/puppet/relay
```
We also have packaging scripts for [Arch Linux](https://github.com/puppetlabs/relay/blob/master/build/package/archlinux/PKGBUILD) and welcome additional packaging contributions!

## Download the workflow

With the CLI installed, you can use it to authenticate to your account and operate on workflows. Running `relay` by itself will show the list of available subcommands, and you can run any subcommand with the `--help` option to get detailed usage information. Log yourself in, get a list of your available workflows, and download the `hello-world` workflow we created earlier to a local file using the following commands:

```
relay auth login yourname@yourdomain.io
relay workflow list
relay workflow download hello-world > hello-world.yaml
```

> Further reading: [Set up your code editor](setting-up-editor.md), [Relay CLI repository](https://github.com/puppetlabs/relay/)

## Modify it to pass data between steps

Relay executes each step in a workflow as a separate container. This allows you to link together smaller, built-to-purpose containers that each accomplish a concrete task rather than putting everything into one big script. Modular components increase reusability, but they do present more of a challenge to pass state information between steps.

Now that we have a single-step workflow running, let's add a step that generates additional input for the "hello world" message. This will illustrate how to use the output from one step as the input to another.

Replace the contents of the workflow file with the following code:

```yaml
parameters:
  message:
    description: "The message to output from the final step"
steps:
- name: generated-output
  image: projectnebula/core
  input:
  - ni output set --key dynamic --value "$(/bin/date)"
- name: hello-world
  image: projectnebula/core
  spec:
    message: !Parameter message
    dynamic: !Output [generated-output,dynamic]
  input:
  - echo "Hello world. Your message was $(ni get -p {.message}), and the generated output was $(ni get -p {.dynamic})"
```

We've added a `generated-output` step which uses the `ni` utility to set the value of a key named `dynamic` with the output of the `/bin/date` program. The spec for the `hello-world` step looks up that value with the `!Output` function and makes it available inside the step for `ni` to read. 

## Update and run it via the CLI

Don't forget to save your work! You've got it stored locally, but you'll need to update the version that lives on the Relay service. Relay keeps your workflows in cloud storage and always runs the latest version you've uploaded. It's a good idea to use git to keep your workflows under revision control using git, but for now we can just replace the previous version with our updates using the CLI:

```
relay workflow replace hello-world -f hello-world.yaml
```

Then we can trigger a run, supplying the `message` parameter using the `-p` flag. When you have a workflow that takes multiple parameters, you can use several `-p` (or `--parameter`, if you really like typing!) flags in a row.

```
relay workflow run hello-world -p message="Run from CLI"
```

The output of this command will include a URL that you can open in your web browser to watch the progress of the run. Under the hood, Relay figured out that, because of the `!Output` tag, the `hello-world` step now has a _dependency_ on the `generated-output` step. So the graph now represents that ordering. You can also use an explicit `dependsOn: step-name` key in a step's definition to force ordering. Without ordering information, Relay will run the steps in parallel to speed up the workflow execution.

> Further reading: [Types reference](reference/relay-types.md), [Complex "Sock Shop" workflow](https://github.com/puppetlabs/nebula-workflow-examples/tree/master/example-workflows/aks-sock-shop).

## Modify Workflow to add a secret

Relay's secret management is one of the most significant advantages of the service over running scripts from your laptop. It keeps sensitive data like passwords and credentials securely stored in Hashicorp Vault and decrypts them for the duration of a workflow run. Eliminating password sprawl and hardcoded API keys reduces risk and increases the reusability of workflows. 

Let's modify our `hello-world` workflow one last time to add a placeholder secret and supply that value. In the web app, edit your `hello-world` step to look like the following:

```yaml
- name: hello-world
  image: projectnebula/core
  spec:
    message: !Parameter message
    dynamic: !Output [generated-output,dynamic]
    supersecret: !Secret mysecret
  input:
  - echo "Hello world. Your message was $(ni get -p {.message}), and the generated output was $(ni get -p {.dynamic})."
  - echo "Normally I would never say this, but your secret was $(ni get -p {.supersecret})"
```
The `ni` utility can access secrets just like regular parameters, but you wouldn't generally want to echo them to the logs as in this example! 
When you click "Save changes", you'll see a warning dialog that you're missing a required secret, and a prompt to fill it in. Follow the link in the prompt to open the Workflow Setup sidebar and enter a secret value. Keep in mind that secrets can be deleted and re-added, but never viewed.

## Next steps...

Now you've got a running example that exercises many useful features of the system:

* the Relay web app shows all the workflows in your account and keeps details logs of their execution history
* the `relay` cli can be used to develop workflows locally, add / delete / replace them on the service, and trigger parameterized runs
* the `!Secret` key and secret management sidebar let you keep sensitive data like API keys and passwords on the service securely.

For next steps, check out the Workflow Library for more real-world examples that you can easily add to your account and modify to suit your needs. Happy automating! 🤖
