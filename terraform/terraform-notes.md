
# Terraform

## Disadvantages of Ansible Compared to Terraform

- Ansible can create, update, and delete infrastructure, but we need to define the required tasks for each   
  operation.
- Ansible is mainly used to automate server configuration and software installation.
- Terraform provides a declarative approach to manage infrastructure based on the desired state (what we want).
- Terraform automatically identifies the changes required to reach the desired state.

## Example

Imagine you want to create the following AWS infrastructure:

- 3 EC2 instances
- 1 Security Group
- 1 Load Balancer
- 1 S3 bucket

You can create these resources manually in the AWS Console, but it takes time.

Using Terraform, you can define everything in configuration files and create the infrastructure automatically.

## What is Terraform?

- Terraform is an open-source **Infrastructure as Code (IaC)** tool.
- It is used to create, manage, update, and delete infrastructure automatically using code instead of manually 
  creating resources through the cloud console.
- Terraform is developed by **HashiCorp**.
