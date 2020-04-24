# Setting up Project Nebula

To set up Nebula, integrate a repository using Nebula's web interface, and install the command-line interface (CLI).

## Integrate a repository with Project Nebula

You must integrate a repository with Nebula before you can add a workflow.

To integrate your repository, log in to the Nebula web interface at [http://nebula.puppet.com/](http://nebula.puppet.com/) and click **Integrations** > **Add Integration**.

## Download and install the Nebula CLI

Use the Nebula command line interface (CLI) to manage your workflows and secrets.

To set up the Nebula CLI:

1.  Download the relevant CLI for your operating system:

    -   [nebula-v3.1.1-darwin-amd64](https://storage.googleapis.com/nebula-releases/nebula-v3.1.1-darwin-amd64)
    -   [nebula-v3.1.1-linux-386](https://storage.googleapis.com/nebula-releases/nebula-v3.1.1-linux-386)
    -   [nebula-v3.1.1-linux-amd64](https://storage.googleapis.com/nebula-releases/nebula-v3.1.1-linux-amd64)
    -   [nebula-v3.1.1-linux-arm64](https://storage.googleapis.com/nebula-releases/nebula-v3.1.1-linux-arm64)
    -   [nebula-v3.1.1-linux-ppc64le](https://storage.googleapis.com/nebula-releases/nebula-v3.1.1-linux-ppc64le)
    -   [nebula-v3.1.1-linux-s390x](https://storage.googleapis.com/nebula-releases/nebula-v3.1.1-linux-s390x)
    -   [nebula-v3.1.1-windows-amd64.exe](https://storage.googleapis.com/nebula-releases/nebula-v3.1.1-windows-amd64.exe)
2.  Rename the file`nebula-cli`. For example:

    ```
    mv nebula-cli-v3.1.1-darwin-amd64 nebula-cli
    ```

3.  Make the file executable:

    ```
    chmod +x nebula-cli
    ```

4.  Log in to Nebula with your username and password:

    ```
    ./nebula-cli login
    ```


## Supported integrations

Project Nebula currently supports the following integrations.

### Source control

-   GitHub
-   Bitbucket
-   GitLab

### Container registries

-   Docker Hub

For more information about triggering a workflow run with Docker Hub, see [Automate your workflows with Docker Hub.](automating-workflow-runs/automate-your-workflows-with-docker-hub.md)

