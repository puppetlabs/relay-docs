# Automate your workflows with Docker Hub

Add a Docker Hub trigger to your workflow to automatically trigger a workflow run each time you push an image to a Docker Hub repository.

**Before you begin**

Make sure you have access to the following:

-   The username and password for the Docker Hub account that you're using to store your application containers. Do not enable two-factor authentication on the Docker Hub account.

    **Note:** Personal access tokens are not currently supported.


Follow these steps to add a Docker Hub trigger to a workflow:

1.  Add a Docker Hub integration:

    1.  From the Relay web interface, click **Integrations** \> **Add Integration** and select **Docker Hub**.

    2.  Enter your Docker Hub username and password, and click **Save**.

2.  Add a trigger to your workflow:

    1.  Click **Workflows**, select the workflow you want to add a trigger to, and click **Edit**.

    2.  Click the **Triggers** tab.

    3.  Click **Define new trigger** and select **Container registry push**.

    4.  Select your Docker Hub trigger integration.

    5.  Enter your repository name. For example, `puppetlabs/k-rad-container`.

    6.  Enter a string or a regular expression (regex) pattern for your image tag. For example, `latest` if you want to use the `latest` tag, or `^prod-` if you're pushing up an image with a `prod-` prefix. Your regex pattern must follow [Google RE2](https://github.com/google/re2/wiki/Syntax) syntax.

3.  Push an image to your Docker Hub repository.

    Pushing an image up to the Docker Hub repository you've linked to your workflow triggers a Relay workflow run.


