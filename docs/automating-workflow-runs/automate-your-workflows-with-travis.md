# Automate your workflows with Travis CI

This scenario uses Travis CI to automate a Relay workflow. You can apply the same principles to automate a workflow using a different CI tool.

Before you begin:

1.  Create a workflow and push it to your source control repository.
2.  Integrate your repository with Relay.
3.  Test the workflow by running it from the Relay CLI or web interface.

Before you add your workflow to Travis, add the following environment variables to your repo from the [Travis web interface](https://travis-ci.com/). Alternatively, [encrypt the variables and add them to your Travis build file using the Travis CLI](https://docs.travis-ci.com/user/environment-variables/#encrypting-environment-variables/).

-   `RELAY_EMAIL`: The email address associated with your Relay account.
-   `RELAY_PASSWORD`: The password associated with your Relay account.

    **Tip:** If you'd prefer to use a shared automation account for your team, set up a user with the **Operator** role using **Access Control** in the Relay web interface.


[Find the correct Relay binary link](../setting-up-relay.md#) for the build environment you're using. For more information on Travis build environments, see [Build Environment Overview](https://docs.travis-ci.com/user/reference/overview/).

To automate your workflow:

1.  Save the following bash script into a `scripts` directory in your workflow repo:

    ```
    #!/bin/bash

    mkdir -p .deploy
    curl -LJ -o .deploy/relay-cli \
        -H 'Accept: application/octet-stream' \
        "https://storage.googleapis.com/relay-releases/relay-v4.0.0-linux-amd64"

    chmod +x .deploy/relay-cli

    echo -n "${RELAY_PASSWORD}" | .deploy/relay-cli login -e "${RELAY_EMAIL}" -p\

    .deploy/relay-cli workflow run -n "my-workflow" -p DeployFromBranch=$TRAVIS_BRANCH
    ```

    The script:

    -   Creates a directory called `.deploy`.

        ```
        mkdir -p .deploy
        ```

    -   Downloads the Relay CLI binary into the directory. If you're using a different build environment, [replace the URL on this line with a link to the correct binary](../setting-up-relay.md#).

        ```
        curl -LJ -o .deploy/relay-cli \
            -H 'Accept: application/octet-stream' \
            "https://storage.googleapis.com/relay-releases/relay-v4.0.0-linux-amd64"
        ```

    -   Makes the binary executable:

        ```
        chmod +x .deploy/relay-cli
        ```

    -   Logs into Relay, passing in the environment variables for your password and email address:

        ```
        echo -n "${RELAY_PASSWORD}" | .deploy/relay-cli login -e "${RELAY_EMAIL}" -p\
        ```

    -   Runs the workflow with any parameters you've declared using the `-p` flag. This example uses the default environment variable `$TRAVIS_BRANCH`. For more information, see [Environment variables](https://docs.travis-ci.com/user/environment-variables/#default-environment-variables).

        ```
        .deploy/relay-cli workflow run -n "my-workflow" -p DeployFromBranch=$TRAVIS_BRANCH
        ```

    **Important:** Make sure the script is executable. To make the script executable, use `chmod +x <YOUR_SCRIPT_NAME>`

2.  Create a Travis build file named `.travis.yml` that includes the script you created. Replace `relay` with the name of your script:

    ```
    jobs:
      include:
      - stage: workflow-run
        script:
        - "./scripts/relay"
    ```

3.  Commit and push your changes.

    Travis clones your repo and executes the script, kicking off a Relay workflow run. You can follow the run's progress from the Relay web interface.