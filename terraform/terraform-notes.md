
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

- Terraform is a **declarative Infrastructure as Code (IaC) tool**.

- Terraform maintains a **State File**, which acts like memory. It keeps track of the infrastructure Terraform  
  has already created and manages.

- Terraform compares the **Desired State** (what the user wants) with the **Current State** (what currently 
  exists) and determines what actions are required.

- It is used to create, manage, update, and delete infrastructure automatically using code instead of manually 
  creating resources through the cloud console.

- Terraform is developed by **HashiCorp**.



# Advantages of Terraform / Infrastructure as Code (IaC)

## 1. State Management

Terraform maintains a State File (`terraform.tfstate`) to track the infrastructure resources it manages.

It compares the current infrastructure with the desired configuration and identifies the changes required.

## 2. Declarative Approach

We define the desired infrastructure, and Terraform identifies what actions are required to achieve it (create, update, or delete).

## 3. Consistent Infrastructure

We can create the same infrastructure across DEV, QA, UAT, and PROD environments, reducing environment-related issues.

## 4. Reusable Infrastructure

Using Terraform Modules, we can reuse the same infrastructure code across different projects and environments.

## 5. Dependency Management

Terraform automatically identifies dependencies between resources and creates them in the correct order.

## 6. CRUD Operations

Terraform supports Create, Read, Update, and Delete operations to manage infrastructure resources.

## 7. Inventory Management

Terraform State File helps us track and identify the infrastructure resources managed by Terraform and understand which resources are currently being used. 

(Inventory Management = Maintaining records of infrastructure resources.)
