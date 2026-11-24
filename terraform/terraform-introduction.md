# Terraform

## Disadvantages of Ansible Compared to Terraform

- Ansible can create, update, and delete infrastructure, but we need to write tasks for each operation.
- Managing infrastructure using Ansible can be complex.
- Ansible is mainly used for server configuration management and application deployment.
- Terraform follows a declarative approach (we define what we want).
- Terraform compares the current state with the desired state and identifies the changes required, such as creating, updating, or deleting resources.

**Understand the Difference:**

- **Current State** → What infrastructure exists now.
- **Desired State** → What infrastructure we want.

---

## Example

Imagine you want to create the following AWS infrastructure:

- 3 EC2 instances
- 1 Security Group
- 1 Load Balancer
- 1 S3 bucket

You can create these resources manually in the AWS Console, but it takes time.

Using Terraform, you can define all the required resources in configuration files and create the infrastructure automatically.

## What is Terraform?

- Terraform is a **declarative Infrastructure as Code (IaC) tool**.

- Terraform maintains a **State File**, which acts like memory. It keeps track of the infrastructure Terraform has already created and manages.

- Terraform compares the **Desired State** (what we want) with the **Current State** (what currently exists) and identifies the changes required.

- It is used to create, manage, update, and delete infrastructure automatically using code instead of creating resources manually through the cloud console.

- Terraform is developed by **HashiCorp**.

- Terraform is **platform independent**. It supports multiple cloud providers and infrastructure platforms.

- `.tf` → Terraform file extension. Files with this extension are called **Terraform configuration files**.