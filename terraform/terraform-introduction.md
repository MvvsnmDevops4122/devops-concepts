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


# Advantages of Terraform / Infrastructure as Code (IaC)

## 1. State Management

Terraform maintains a State File (`terraform.tfstate`) to track the infrastructure resources it manages.

It compares the current state with the desired configuration and identifies the changes required.

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

# Terraform Provider

- In Terraform, a **Provider** is a plugin that allows Terraform to communicate with cloud platforms and manage their infrastructure.

- The Terraform AWS Provider communicates with AWS by calling AWS APIs to create, read, update, and delete resources.

- Before creating infrastructure, we need to configure AWS credentials. We can configure them using the AWS CLI. Then, we configure the required provider in Terraform.

### Examples

- **AWS Provider** → Communicates with AWS and manages its resources.
- **Azure Provider** → Communicates with Microsoft Azure and manages its resources.
- **Google Cloud Provider** → Communicates with Google Cloud and manages its resources.

### Example: AWS Provider

Create a file named `providers.tf`.

```hcl
# Configure AWS Provider
provider "aws" {
  region = "us-east-1"
}
```

### Explanation

- `provider` → Terraform block used to configure a provider.
- `"aws"` → Provider name.
- `region` → AWS region where you want to create resources.
- `"us-east-1"` → N. Virginia region.

## Installing the AWS Provider Plugin

```bash
# Initialize the Terraform working directory
# Download the required provider plugins
terraform init
```

**Note:** `terraform init` downloads the required provider plugins based on the provider configuration in your Terraform files (e.g., `providers.tf`).

After running `terraform init`, Terraform automatically creates the following:

1. **`.terraform/` directory** → Stores the downloaded provider plugins and other initialization files.
2. **`.terraform.lock.hcl` file** → Records the selected provider versions and checksums to ensure consistent provider versions across future runs.


## HCL (HashiCorp Configuration Language)

- HCL stands for **HashiCorp Configuration Language**.
- HCL is the configuration language used to write Terraform code.
- Terraform uses blocks and arguments to define infrastructure configurations.
- Most Terraform configurations use curly braces `{ }` to define blocks.

### Basic Syntax

```hcl
<type-of-block> "label" {
  key = "value"
}
```

### Types of Terraform Blocks

1. **Resource** – Creates and manages infrastructure resources.
2. **Variable** – Defines input variables.
3. **Provider** – Configures the cloud provider.
4. **Output** – Displays information after Terraform execution.

**Examples of AWS Resources:**

EC2, S3, VPC, Security Group, Load Balancer, RDS, EBS.

## Resource Block

### Syntax

```hcl
resource "<resource-type>" "<resource-name>" {
  key = value
}
```

### Example

```hcl
resource "aws_instance" "roboshop-mongodb" {
  ami           = "ami-1234"
  instance_type = "t3.micro"

  tags = {
    Name = "roboshop-mongodb"
  }
}
```

### Explanation

- `resource` → Block type.
- `{ }` → Block body, where we define resource configuration.
- `"aws_instance"` → Resource type (AWS EC2 instance).
- `"roboshop-mongodb"` → Resource label (name used to identify the resource in Terraform code).
- `ami` → Specifies the AMI ID used to launch the EC2 instance.
- `instance_type` → Specifies the EC2 instance type.
- `tags` → Used to assign tags to the EC2 instance.
- `Name` → Tag key.
- `"roboshop-mongodb"` → Tag value (name displayed in the AWS Console).


# Terraform State Files

- Terraform State is a file that stores information about the infrastructure managed by Terraform.

- It helps Terraform compare the desired configuration with the current state, track existing resources, and identify what needs to be created, updated, or deleted.

- By default, Terraform stores the state locally in a file named `terraform.tfstate`.

### State Files Created by Terraform

After running `terraform apply` for the first time, Terraform creates the following files:

1. **`terraform.tfstate`** → Stores the current state of infrastructure managed by Terraform.

2. **`terraform.tfstate.backup`** → Stores a backup of the previous state file when Terraform updates the state.

**Note:** These files are automatically managed by Terraform. Do not manually edit or upload them to a public GitHub repository because they may contain sensitive information.

# Why Do We Use Terraform State?

Imagine we have created the following AWS infrastructure using Terraform:

- 2 EC2 instances
- 1 Security Group
- 1 S3 bucket

Terraform stores information about these resources in the state file.

Terraform State helps to:

- Track infrastructure resources managed by Terraform.
- Identify existing resources.
- Understand which resources need to be created, updated, or deleted.

