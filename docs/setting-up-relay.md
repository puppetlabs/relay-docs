# Setting up Relay 

To set up Relay, integrate a repository using Relays's web interface, and install the command-line interface (CLI).

## Integrate a repository with Relay

You must integrate a repository with Relay before you can add a workflow.

To integrate your repository, log in to the Relay web interface at [https://nebula.puppet.com/](https://nebula.puppet.com/) and click **Integrations** > **Add Integration**.

## Download and install the Relay CLI

Use the Relay command line interface (CLI) to manage your workflows and secrets.

To set up the Relay CLI: (TODO Rework this whole section)

1.  Download the relevant CLI for your operating system:

2.  Log in to Relay with your username and password:

    ```
    ./relay auth
    ```


## Supported integrations

Relay currently supports the following integrations.

### Source control

-   GitHub
-   Bitbucket
-   GitLab

### Container registries

-   Docker Hub

For more information about triggering a workflow run with Docker Hub, see [Automate your workflows with Docker Hub.](automating-workflow-runs/automate-your-workflows-with-docker-hub.md)

