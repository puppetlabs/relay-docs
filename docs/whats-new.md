# What's new in Relay?

## April 2020

We've got a new name! You may still see references to the pre-launch name of Relay, "Project Nebula", around the site and source code but we're officially going to be called Relay. (The previous entries in this changelog will retain the earlier name.)

## February 2020

**Conditional step execution**

We've added the ability to execute steps based on conditional logic. You can now use the `when` keyword, together with the `equals` and `notEquals` functions inside a step to control whether Nebula should execute the step. For more information, see [Conditional step execution](using-workflows/conditionals.md).

**Example workflows**

**EC2 Reaper**: At Puppet, we need to ensure that the company’s usage of the public cloud is monitored and tracked appropriately. In order to provide this governance over AWS specifically, we created the "AWS Reaper" application in-house. To control cost, Reaper scans the company's AWS resource tags and destroys any resources with expired or inaccurate tags. This workflow automates this process in Nebula, identifying EC2 resources that have not been tagged properly and de-provisioning them. For more information, see [EC2 Reaper](https://github.com/puppetlabs/nebula-workflow-examples/blob/master/example-workflows/ec2-reaper/README.md).

## January 2020

**Workflow Automation with Docker Hub**

You can now automatically trigger a workflow run each time you push an image to Docker Hub. For more information, see [Automate your workflow runs with Docker Hub](automating-workflow-runs/automate-your-workflows-with-docker-hub.md).

**GitLab Integration**

Connect your GitLab source control repos to store and access your Nebula workflows from GitLab.

**Example workflows**

**Deploy Sock Shop demo to Azure Kubernetes Service (AKS):** This sample workflow deploys the Weavework’s Sock Shop demo to an Azure Kubernetes Service (AKS) cluster. For more information, see [the workflow README](https://github.com/puppetlabs/nebula-workflow-examples/tree/master/example-workflows/aks-sock-shop).

**Deploy a simple application to Amazon EKS:** This sample workflow uses CloudFormation to provision IAM roles, a VPC, an EKS cluster, and worker nodes. You then deploy a container to this newly created infrastructure and alert your team of a successful deployment. For more information, see [the workflow README](https://github.com/puppetlabs/nebula-workflow-examples/tree/master/example-workflows/eks-provision-and-deploy-workflow).

## December 2019

**Approval steps**

Workflow runs now support your organization's manual review process with approval steps. This allows teams to follow development best practices, adhere to security policies, and support cross-team communication on every deployment. For more information, see [Adding an approval step to a workflow](adding-an-approval-step.md).

**Workflow YAML improvements**

Project Nebula workflows now support simplified YAML syntax for secrets, parameters, and outputs. We've also added support for `append`, `concat`, `jsonUnmarshal` and `merge` functions. For more information, see our [Reference docs](reference.md).

**User experience improvements**

-   Reduced workflow run pending times

-   Sort and filter your workflows


