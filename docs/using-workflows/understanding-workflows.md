# Understanding workflows

Project Nebula uses YAML workflows to define the steps in an application deployment.

Each step in a Nebula workflow performs a distinct action and runs in its own container. You can use steps to provision resources, deploy applications, or execute tasks.

When you run a workflow, Nebula executes each step using a Tekton pipeline. Nebula executes all steps simultaneously unless you explicitly define a sequential order for the steps to run in. For more information on explicit step ordering, see [Writing a workflow](create-workflow.md).

