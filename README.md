# Description
This repo contains files in support of an integration between Sophos Factory and Terraform Cloud. The goal is to insert a **Run Task** in the Terraform Cloud workflow that runs a Sophos Factory job configured with a WebHook trigger which will subsequently execute a Sophos factory pipeline which could be configured to execute any number of tasks, such as an ```opa eval``` scan, Terrascan, or Checkov scan and then send the result back to Terraform Cloud's calling **Run Task**.

The basic flow looks like this:

* Terraform Cloud Run
* Terraform Cloud Run Task
* Sophos Factory Job (WebHook Trigger)
* Sophos Factory Pipeline
* Reply Back to Terraform Cloud Run Task
* Finish Terraform Cloud Workflow

The inspiration for this example comes from the [Open Policy Agent | Terraform](https://www.openpolicyagent.org/docs/latest/terraform/) use case. The files listed in the instructions above have been added to this repo.

## Requirements
* A Terraform Cloud account 
* A Sophos Factory account 
* An AWS user with 
    * An Access Key 
    * Permission to do a plan based on the Terraform configuration in this repo

## Instructions
* In AWS
    * If you don't already have one, create an IAM user with programmatic access and have the access key id and secret access key available for later
    * Assign the user the appropriate permissions to deploy the Terraform configuration in this repo
        * (you do not need to deploy any resources to AWS in order to prove the workflow was successful)
* In Sophos Factory
    * Create a new Job using the following settings:
        * Trigger: **Incoming WebHook**
        * Webhook Type: **Terraform Cloud Run Task**
        * (Optional) Terraform Secret Token: (A Generic Secret credential with an HMAC value)
        * Variables Transform: ```{"data": body | dump}```
        * Pipeline Options:
            * Catalog: *choose one of the following example pipelines*
                * **Terraform Cloud Run Task | OPA Eval**
                * **Terraform Cloud Run Task | Terrascan**
                * **Terraform Cloud Run Task | Checkov**
        * Revision: **latest**
    * Click *Create*
    * Copy the WebHook URL for use later
* In Terraform Cloud
    * Create a new Event Hook (YourOrg ➡ Settings ➡ Task event hooks ➡ Create event hook)
        * Name: *Choose a descriptive name*
        * Endpoint URL: *Paste in the WebHook URL from the Sophos Factory job*
        * HMAC (optional): *Paste in the HMAC used earlier*
    * Create a new workspace (if one does not already exist) in Terraform Cloud using the following settings:
        * Version Control Workflow
            * Provider: **GitHub**
            * ID: ```refactr/opa-terraform-demo```
        * Add the new Event Hook (YourWorkspace ➡ Settings ➡ Tasks)
            * Enforcement Level: *choose either value*
    * Add the following Environment Variables (YourWorkspace ➡ Variables ➡ Environment Variables)
        * AWS_ACCESS_KEY_ID: *Paste in your AWS user's access key id*
        * AWS_SECRET_ACCESS_KEY: *Paste in your AWS user's secret access key*
        * AWS_DEFAULT_REGION: *choose an AWS region*
    * Start a new plan to confirm everything works (YourWorkspace ➡ Actions ➡ Start a new plan)
        * Enter a reason for the new plan
        * Click **Start plan**
            * If you see a green check mark and **Tasks Passed** then you have successfully completed this integration!
            * Feel free to cancel the plan

## Resources
* [Sophos Factory | Community Edition](https://www.refactr.it/community-edition)
* [Sophos Factory | Webhook Job Trigger](https://docs.refactr.it/docs/running-pipelines/#incoming-webhook-job-trigger)
* [Terraform Cloud | Create Account](https://app.terraform.io/signup/account)
* [Terraform Cloud | Run Tasks](https://www.terraform.io/docs/cloud/workspaces/run-tasks.html)
* [Terraform Cloud | Run Tasks API](https://www.terraform.io/docs/cloud/api/run-tasks.html)
* [AWS | Create Account](https://aws.amazon.com/resources/create-account/)
* [Open Policy Agent | Terraform](https://www.openpolicyagent.org/docs/latest/terraform/)

