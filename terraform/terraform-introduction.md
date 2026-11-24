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
