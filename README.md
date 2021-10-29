# Description
This repo contains files in support of an integration between the Refactr Platform and Terraform Cloud. The goal is to insert an intermediary step in the Terraform Cloud workflow by way of a Refactr Pipeline that can execute any number of tasks, such as an ```opa eval``` scan, Terrascan, or Checkov scan using Terraform Cloud's new **Event Hook** feature to trigger a Refactr pipeline via WebHook.

The basic flow looks like this:

Terraform Plan ➡ Event Hook ➡ Refactr WebHook ➡ Refactr Pipeline ➡ Back to Event Hook ➡ Deploy

The inspiration for this example comes from the [Open Policy Agent | Terraform](https://www.openpolicyagent.org/docs/latest/terraform/) use case. The files listed in the instructions above have been added to this repo.

## Requirements
* A Terraform Cloud account 
* A Refactr Platform account 
* An AWS user with 
    * An Access Key 
    * Permission to do a plan based on the Terraform configuration in this repo

## Instructions
* In AWS
    * If you don't already have one, create an IAM user with programmatic access and have the access key id and secret access key available for later
    * Assign the user the appropriate permissions to deploy the Terraform configuration in this repo
        * (you do not need to deploy any resources to AWS in order to prove the workflow was successful)
* In the Refactr Platform
    * Create a new Job using the following settings:
        * Trigger: **Incoming WebHook**
        * Variables Transform: ```{"data": body | dump}```
        * Pipeline: *choose one of the following example pipelines*
            * **Terraform Cloud Event Hook | OPA Eval**
            * **Terraform Cloud Event Hook | Terrascan**
            * **Terraform Cloud Event Hook | Checkov**
        * Revision: **latest**
    * Copy the WebHook URL for use later
* In Terraform Cloud
    * Create a new Event Hook (YourOrg ➡ Settings ➡ Task event hooks ➡ Create event hook)
        * Name: *Choose a descriptive name*
        * Hook endpoint URL: *Paste in the WebHook URL from the Refactr job*
    * Create a new workspace in Terraform Cloud using the following settings:
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
* [Create a Terraform Cloud account](https://app.terraform.io/signup/account)
* [Refactr Community Edition](https://www.refactr.it/community-edition)
* [Create AWS Account](https://aws.amazon.com/resources/create-account/)
* [Refactr Webhook Job Trigger](https://docs.refactr.it/docs/running-pipelines/#incoming-webhook-job-trigger)
* [Open Policy Agent | Terraform](https://www.openpolicyagent.org/docs/latest/terraform/)
* [Run Tasks](https://www.terraform.io/docs/cloud/workspaces/run-tasks.html)
* [Run Tasks API](https://www.terraform.io/docs/cloud/api/run-tasks.html)