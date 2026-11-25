
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

# Terraform

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
#(Initialize the Terraform directory means preparing the current working directory to run Terraform commands.)
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

# Terraform Variables

Terraform variables are used to store values and make Terraform code reusable.

Instead of hardcoding values, we define variables in `variables.tf` and assign values through `terraform.tfvars`, command-line options, prompts, or environment variables.

## Types of Terraform Variables / Data Types

Terraform supports different variable types.

| Type | Example |
|---|---|
| string | `"t3.micro"` |
| number | `2` |
| bool | `true` |
| list | `["mongodb", "redis"]` |
| map | `{ dev = "t3.micro", prod = "t3.small" }` |
| object | `{ name = "web", size = "t3.micro" }` |

### Examples

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

## How to Define Variables?

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

### Explanation

- `variable` → Keyword used to define a variable.
- `"ami_id"` → Variable name.
- `default` → Default value assigned to the variable.

## How to Use Variables?

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

### Explanation

- `var` → Used to access a Terraform variable.
- `var.ami_id` → Gets the value of the `ami_id` variable.
- `var.instance_type` → Gets the value of the `instance_type` variable.

## Assign Values Using terraform.tfvars

Create a file named `terraform.tfvars`.

**terraform.tfvars**

```hcl
ami_id        = "ami-123456"
instance_type = "t3.micro"
```

- Terraform automatically loads values from a file named `terraform.tfvars`.
- We don't need to specify the file name when executing Terraform commands.

**Note:** If we use a custom variable file name like `dev.tfvars`, we need to pass it using `-var-file`.

```bash
terraform plan -var-file="dev.tfvars"
terraform apply -var-file="dev.tfvars"
```

**Remember:**

- `terraform.tfvars` → Automatically loaded.
- `dev.tfvars` → Must be specified using `-var-file`.
- `dev.auto.tfvars` → Automatically loaded.

## Assign Values Using Command Line

We can also pass variable values using the `-var` option.

```bash
# Pass the instance type through the command line
terraform plan -var="instance_type=t3.small"

# Apply using a variable value
terraform apply -var="instance_type=t3.small"
```

## Terraform Environment Variables

Terraform supports environment variables using the `TF_VAR_` prefix.

We can pass variable values through environment variables instead of writing them in `terraform.tfvars`.

### 1. Linux / macOS / Git Bash

```bash
# Set Terraform variable
export TF_VAR_instance_type="t3.small"
unset TF_VAR_instance_type
```

### 2. Windows PowerShell

```powershell
# Set Terraform variable
$env:TF_VAR_instance_type = "t3.small"
```

### 3. Windows CMD

```cmd
:: Set Terraform variable
set TF_VAR_instance_type=t3.small

:: Remove the environment variable
set TF_VAR_instance_type=
```

### 4. Use Variable

**main.tf**

```hcl
resource "aws_instance" "web" {
  instance_type = var.instance_type
}
```

### 5. Execute Terraform Command

```bash
# Preview infrastructure changes
terraform plan
```

Terraform automatically reads the environment variable and uses `t3.small` instead of the default value `t3.micro`.

## How Do You Pass Variables Using Environment Variables in Terraform?

We can pass variables using the `TF_VAR_` prefix.

For example:

```bash
export TF_VAR_instance_type="t3.small"
```

This sets the Terraform variable `instance_type` to `t3.small`.

## Terraform Command line Variables

Variable value pass through command line: 

terraform plan -var="instance_type=t3.small"
terraform apply -var="instance_type=t3.small"


## Terraform Variable Precedence

If the same variable receives values from multiple sources, Terraform follows a precedence order.

From highest priority to lowest:

1. **Command-line options** → `-var` and `-var-file`
2. **terraform.tfvars files** → `terraform.tfvars` and `terraform.tfvars.json`
3. **Environment variables** → `TF_VAR_name`
4. **Default value** → Value defined inside the variable block
5. **Prompt**

**Note:** If no value or default is provided for a required variable, Terraform prompts us to enter a value when running interactively.

## Terraform Variable Validation

Variable validation is used to check whether the given variable value is correct.

It helps prevent incorrect values from being used in Terraform.

### Example

**variables.tf**

```hcl
variable "environment" {
  type    = string
  default = "dev"

  validation {
    condition     = contains(["dev", "uat", "prod"], var.environment)
    error_message = "Environment must be dev, uat, or prod."
  }
}
```

### Explanation

- `validation` → Nested block used to validate the variable value.
- `condition` → Checks whether the value is correct.
- `contains()` → Terraform function that checks whether a given value exists in a list.
- `error_message` → Displays an error if the condition fails.

**Allowed values:** `dev`, `uat`, `prod`

If we provide `test`, Terraform displays the error message.

# Terraform Condition

A condition is used to check whether an expression is true or false.

In Terraform, conditional expressions allow us to select one value or another based on a condition.

## Syntax

```hcl
condition ? true_value : false_value
```

## Example

**main.tf**

```hcl
variable "environment" {
  default = "dev"
}

resource "aws_instance" "web" {
  ami = "ami-123456"

  instance_type = var.environment == "prod" ? "t3.medium" : "t3.micro"
}
```

**Explanation:**

- `==` checks whether two values are equal.
- `?` means if the condition is true.
- `:` means if the condition is false.

**Output:**

- If environment is `prod`, instance type is `t3.medium`.
- Otherwise, instance type is `t3.micro`.













## Terraform Output

- **Output** blocks are used to display useful information about resources after Terraform execution.
- We can use output values to retrieve resource details such as EC2 instance ID, public IP, private IP, and DNS name.

### Example (outputs.tf)

```hcl
# Display EC2 instance ID
output "instance_id" {
  value = aws_instance.roboshop.id
}

# Display EC2 public IP
output "public_ip" {
  value = aws_instance.roboshop.public_ip
}

# Display EC2 private IP
output "private_ip" {
  value = aws_instance.roboshop.private_ip
}
```

### Commands

```bash
terraform apply   # Create infrastructure and display output values
terraform output  # Display all output values
```

## Terraform Functions

- In Terraform, we cannot define our own custom functions. We can use built-in Terraform functions.
- Functions take input values, perform an operation, and return an output value.
- Functions help us work with strings, numbers, lists, maps, and other data.

### Function Syntax

```hcl
function_name(input)  # Returns output
```

### 1. length()

- `length()` calculates the number of items in a list or characters in a string.

**Example:**

```hcl
length(["apple", "banana", "pomo", "guava", "grapes"])

# Output: 5
```

### 2. contains()

- `contains()` checks whether a given value exists in a list.
- Returns `true` if the value exists; otherwise, returns `false`.

**Example:**

```hcl
fruits = ["apple", "banana", "pomo", "guava", "grapes"]

contains(fruits, "watermelon")  # Output: false
contains(fruits, "pomo")        # Output: true
```

### 3. index()

- `index()` returns the position (index number) of a given value in a list.
- Index numbering starts from `0`.
- If the value does not exist, Terraform returns an error.

**Example:**

```hcl
fruits = ["apple", "banana", "pomo", "guava", "grapes"]

index(fruits, "banana")  # Output: 1
index(fruits, "berries") # Error: Value not found
```

**Simple Understanding:** Terraform functions take input, perform an operation, and return an output.





