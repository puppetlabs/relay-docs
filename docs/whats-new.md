# What's new in Relay?

## June 2020

**New workflows**
We've added a bunch of new [example workflows](https://relay.sh/workflows) that you can run with one click. They span across categories such as cost optimization, security, incident response and more. Here are a couple examples:

- [When a Datadog alert is triggered, create a Jira ticket](https://relay.sh/workflows/datadog-to-jira/)
- [Run Terraform when a Pull Request is merged in GitHub](https://relay.sh/workflows/terraform-continuous-deployment/)
- [Restrict public S3 buckets with WRITE permissions](https://relay.sh/workflows/s3-restrict-public-write-buckets/)

If there's a workflow you'd like to see, [let us know](mailto:relay@puppet.com).

**New Integrations**
We're also weekly adding more and more [integrations](https://relay.sh/integrations) for different tools and services. We now support (the beginnings) of AWS, Azure, and GCP cloud providers, incident response integrations such as PagerDuty and VictorOps, Datadog, Jira, Github and more. [Check it out!](https://relay.sh/integrations)

## May 2020

**Integration webhook triggers** 

Integrations now support webhook triggers. Relay will create a webserver listening for incoming webhooks from other services and execute integration code in response. [Here's an example](https://github.com/relay-integrations/relay-github/tree/master/triggers/pull-request-merged/handler.py) of the code that Relay will run after receiving a `merge` event from a GitHub PR webook. You can contribute to an [existing integration](https://github.com/relay-integrations/relay-github) or use your own container to handle the incoming webhook! 

You'll start to see a lot more integrations featuring webhook triggers for a wide variety of services that support outgoing webhooks. 

**Connections**

Connections are a new feature within Relay that allow you to link Relay to other services you use. One cool thing about connections is that they can be re-used across workflows – so configuring a second workflow that uses that connection is automatic. [Find out more about connections here](./using-workflows/adding-connections.md).

## April 2020

We've got a new name! You may still see references to the pre-launch name of Relay, "Project Nebula", around the site and source code but we're officially going to be called Relay. (The previous entries in this changelog will retain the earlier name.)

## February 2020

**Conditional step execution**

We've added the ability to execute steps based on conditional logic. You can now use the `when` keyword, together with the `equals` and `notEquals` functions inside a step to control whether Nebula should execute the step. For more information, see [Conditional step execution](./using-workflows/conditionals.md).

**Example workflows**

**EC2 Reaper**: At Puppet, we need to ensure that the company’s usage of the public cloud is monitored and tracked appropriately. In order to provide this governance over AWS specifically, we created the "AWS Reaper" application in-house. To control cost, Reaper scans the company's AWS resource tags and destroys any resources with expired or inaccurate tags. This workflow automates this process in Nebula, identifying EC2 resources that have not been tagged properly and de-provisioning them. For more information, see [EC2 Reaper](https://relay.sh/workflows/ec2-reaper).

## December 2019

**Approval steps**

Workflow runs now support your organization's manual review process with approval steps. This allows teams to follow development best practices, adhere to security policies, and support cross-team communication on every deployment. For more information, see [Adding an approval step to a workflow](using-workflows/adding-an-approval-step.md).

**Workflow YAML improvements**

Project Nebula workflows now support simplified YAML syntax for secrets, parameters, and outputs. We've also added support for `append`, `concat`, `jsonUnmarshal` and `merge` functions. For more information, see our [Reference docs](reference.md).

**User experience improvements**

-   Reduced workflow run pending times

-   Sort and filter your workflows
