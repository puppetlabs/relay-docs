# Understanding workflows

Relay uses YAML workflows to define the steps in an application deployment.

Each step in a Relay workflow performs a distinct action and runs in its own container. You can use steps to provision resources, deploy applications, or execute tasks.

When you run a workflow, Relay executes each step using a Tekton pipeline. Relay executes all steps simultaneously unless you explicitly define a sequential order for the steps to run in. For more information on explicit step ordering, see [Writing a workflow](create-workflow.md).

