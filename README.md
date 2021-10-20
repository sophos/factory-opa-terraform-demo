# Description
This repo contains files in support of the Refactr Pipeline OPA Eval. The purpose of this pipeline is to demonstrate how to trigger an OPA scan of a Terraform configuration via a Refactr WebHook from Terraform Cloud using a Terraform Event Hook.

The inspiration for this example comes from the [Open Policy Agent | Terraform](https://www.openpolicyagent.org/docs/latest/terraform/) use case. The files listed in the instructions above have been added to this repo.

## Requirements
* A Terraform Cloud account [Create an Account](https://app.terraform.io/signup/account)
* A Refactr Platform account [Community Eddition](https://www.refactr.it/community-edition)


## Instructions
* Create a new Job in the Refactr Platform using the following settings:
    * Trigger: Incoming WebHook
    * Variables Transform:
        ```
        {
        "data": body | dump
        }
        ```
    * Pipeline: OPA Eval
    * Revision: latest

* Create a new workspace in Terraform Cloud using the following settings:
    * Version Control Workflow
    * GitHub for the version control provider with the id of ```refactr/opa-terraform-demo```
* Create a new Event Hook by navigate to Settings tab on the top navigation bar, and select Task E


## Resources
[Open Policy Agent | Terraform](https://www.openpolicyagent.org/docs/latest/terraform/)