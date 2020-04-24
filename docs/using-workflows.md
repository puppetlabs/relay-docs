# Using workflows

Project Nebula uses YAML workflows to define the steps in an application deployment.

Each step in a Nebula workflow runs in a separate container. Nebula executes the steps 
simultaneously unless you explicitly define a sequential order for the steps to run in.

-   **[Understanding workflows](using-workflows/understanding-workflows.md)**  
-   **[Writing a workflow](using-workflows/create-workflow.md)**  
-   **[Running a workflow](using-workflows/running-a-workflow.md)**  
-   **[Adding secrets](using-workflows/adding-secrets.md)**  
-   **[Passing data into workflow steps](using-workflows/passing-data-into-workflow-steps.md)**