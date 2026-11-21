
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

- Terraform is platform independent.

- `.tf` → Terraform file extension. Files with this extension are called **Terraform configuration files**.

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


## Provider

- In Terraform, a **Provider** is a plugin that allows Terraform to communicate with cloud platforms and manage  
  their resources.

- The Terraform AWS Provider communicates with AWS by calling AWS APIs to create, read, update, and delete 
  resources.

- Before creating infrastructure, we need to configure AWS credentials. We can configure them using the AWS CLI. 
  Then, we need to configure the required provider in Terraform.

### Examples

- **AWS Provider** → Communicates with AWS and manages its resources.
- **Azure Provider** → Communicates with Microsoft Azure and manages its resources.
- **Google Provider** → Communicates with Google Cloud and manages its resources.

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

**Note:** `terraform init` downloads the required provider plugins based on the provider configuration in your Terraform files (e.g., `provider.tf`).

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
2. **Data** – Reads information about existing infrastructure.
3. **Variable** – Defines input variables.
4. **Provider** – Configures the cloud provider.
5. **Output** – Displays information after Terraform execution.
6. **Module** – Groups reusable Terraform configurations.

Resources = EC2, S3, VPC, Security Group, Load Balancer, RDS, EBS.

### Example:

resource "aws_instance" "roboshop-mongodb" {
  ami           = "ami-1234"
  instance_type = "t3.micro"

  tags = {
    Name = "roboshop-mongodb"
  }
}


### Explanation

- `resource` → Block type.
- { } → Block body 
- `"aws_instance"` → Resource type (EC2 instance).
- `"roboshop-mongodb"` → → **Resource label.** A name used to identify the resource in Terraform code(terraform 
    resource name).
- `tags` → Used to assign tags to the EC2 instance.
- `Name` → Tag key.
- `"roboshop-mongodb"` → Tag value (name displayed in AWS console)(AWS resouce name).


# Terraform Commands

## 1. Version

```bash
# Check the installed Terraform version
terraform -version
```

## 2. Initialize

```bash
# Initialize the Terraform working directory
# Download the required provider plugins
terraform init
```

## 3. Plan

```bash
# Preview the infrastructure changes
# Shows what Terraform will create, update, or destroy
terraform plan
```

## 4. Apply

```bash
# Create or update infrastructure based on the configuration
terraform apply

# Create infrastructure without asking for confirmation
terraform apply -auto-approve
```

## 5. Destroy

```bash
# Delete the infrastructure (asks for confirmation)
terraform destroy
# Delete infrastructure without asking for confirmation
terraform destroy -auto-approve
```

# Terraform State Files

- Terraform State is a file that stores information about the infrastructure created by Terraform. It 
  helps Terraform track existing resources and understand what needs to be created, updated, or deleted.

- By default, Terraform stores state locally in a file named `terraform.tfstate`.


- After running `terraform apply`, Terraform automatically creates the following files:

1. **terraform.tfstate** → Stores the current state of infrastructure managed by Terraform.

2. **terraform.tfstate.backup** → Stores a backup of the previous state file for recovery if needed.

**Note:** These files are automatically managed by Terraform. Do not manually edit or upload them to a public GitHub repository because they may contain sensitive information.


# Why do we use Terraform State?

Imagine we have created the following AWS infrastructure using Terraform:

- 2 EC2 instances
- 1 Security Group
- 1 S3 bucket

Terraform stores information about these resources in the state file.

Terraform State helps to:

- Track created infrastructure.
- Identify existing resources.
- Understand which resources need to be created, updated, or deleted.
- Map Terraform configuration resources to real infrastructure resources.
- Detect infrastructure changes (drift).


# Terraform Variables

Terraform variables are used to store values and make Terraform code reusable. Instead of hardcoding values, we define variables in `variables.tf` and assign values through `terraform.tfvars` or command-line options.

##  Types of Terraform Variables/datatypes

Terraform supports different variable types.

| Type | Example |
|---|---|
| string | `"t3.micro"` |
| number | `2` |
| bool | `true` |
| list | `["mongodb", "redis"]` |
| map | `{ dev = "t3.micro", prod = "t3.small" }` |
| object | `{ name = "web", size = "t3.micro" }` |

Example:

```hcl
# String variable
variable "environment" {
  type    = string
  default = "dev"
}

# Number variable
variable "instance_count" {
  type    = number
  default = 2
}

# Boolean variable
variable "enable_monitoring" {
  type    = bool
  default = true
}
```

NOTE: If we use a custom variable file name like dev.tfvars, we need to pass it using terraform plan -var-file="dev.tfvars" or terraform apply -var-file="dev.tfvars".

Remember:

- terraform.tfvars – Automatically loaded.
- dev.tfvars – Must be specified using -var-file.


##  How to Define Variables?

Create a file named `variables.tf`.

**variables.tf**

```hcl
# Variable for EC2 AMI ID
variable "ami_id" {
  default = "ami-123456"
}

# Variable for EC2 instance type
variable "instance_type" {
  default = "t3.micro"
}
```

Explanation:

- `variable` - Keyword used to define a variable.
- `"ami_id"` - Variable name.
- `default` - Default value assigned to the variable.

##  How to Use Variables?

Create a file named `main.tf`.

**main.tf**

```hcl
# AWS provider configuration
provider "aws" {
  region = "us-east-1"
}

# Create an EC2 instance
resource "aws_instance" "web" {
  ami           = var.ami_id
  instance_type = var.instance_type
}
```

##  Assign Values Using terraform.tfvars

Create a file named `terraform.tfvars`.

**terraform.tfvars**

```hcl
ami_id        = "ami-123456"
instance_type = "t3.micro"
```

- Terraform automatically loads values from a file named `terraform.tfvars`.
- We don't need to specify the file name when executing Terraform commands.


##  Assign Values Using Command Line

We can also pass variable values using the `-var` option.

```bash
# Pass the instance type through the command line
terraform plan -var="instance_type=t3.small"

# Apply using a variable value
terraform apply -var="instance_type=t3.small"
```

# Terraform Environment Variables

Terraform supports environment variables using the `TF_VAR_` prefix.

We can pass variable values through environment variables instead of writing them in `terraform.tfvars`.

## 1. Linux / macOS (Git Bash)

```bash
# Set Terraform variable
export TF_VAR_instance_type="t3.small"
```

## 2. Windows PowerShell

```powershell
# Set Terraform variable
$env:TF_VAR_instance_type = "t3.small"
```

## 3. Windows CMD

```cmd
:: Set Terraform variable
set TF_VAR_instance_type=t3.small

unset TF_VAR_instance_type
```

## 4. Use Variable

**main.tf**

```hcl
resource "aws_instance" "web" {
  instance_type = var.instance_type
}
```

## 5. Execute Terraform Command

```bash
# Preview infrastructure changes
terraform plan
```

Terraform automatically reads the environment variable and uses `t3.small` instead of the default value `t3.micro`.


# How do you pass variables using environment variables in Terraform?**

We can pass variables using the `TF_VAR_` prefix. For example, `export TF_VAR_instance_type="t3.small"` sets the Terraform variable `instance_type` to `t3.small`.


# Terraform Variable Validation

Variable validation is used to check whether the given variable value is correct or not.

It helps prevent incorrect values from being used in Terraform.

## Example

**variables.tf**

```hcl
variable "environment" {
  default = "dev"

  validation {
    condition     = contains(["dev", "uat", "prod"], var.environment)
    error_message = "Environment must be dev, uat, or prod."
  }
}
```

**Explanation:**

- `validation` - Used to validate the variable.
- `condition` - Checks whether the value is correct.
- `contains()` - Checks whether the value exists in the list.
- `error_message` - Displays an error if the condition fails.

**Allowed values:** `dev`, `uat`, `prod`

If we provide `test`, Terraform displays the error message.


# Terraform Variable Precedence

If the same variable receives values from multiple sources, Terraform follows a precedence order.

From highest priority to lowest:

1. `-var` and `-var-file` command-line options (command-line order)
2. `terraform.tfvars` or `terraform.tfvars.json`
3. Environment variables (`TF_VAR_name`)
4. Variable default value
5. prompt

**Example:**

If the same variable has different values:

- Default value = `t3.micro`
- Environment variable = `t3.small`
- `terraform.tfvars` = `t3.medium`
- Command line = `t3.large`

```bash
terraform plan -var="instance_type=t3.large"
```

**Final value:** `t3.large`

The command-line value has the highest priority.

Refer: learn-terraform/02-variables


