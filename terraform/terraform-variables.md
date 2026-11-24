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

- `validation` → Used to validate the variable.
- `condition` → Checks whether the value is correct.
- `contains()` → Terraform function that checks whether a given value exists in a list.
- `error_message` → Displays an error if the condition fails.

**Allowed values:** `dev`, `uat`, `prod`

If we provide `test`, Terraform displays the error message.

## Terraform Variable Precedence

If the same variable receives values from multiple sources, Terraform follows a precedence order.

From highest priority to lowest:

1. **Command-line options** → `-var` and `-var-file`
2. **terraform.tfvars files** → `terraform.tfvars` and `terraform.tfvars.json`
3. **Environment variables** → `TF_VAR_name`
4. **Default value** → Value defined inside the variable block
5. **Prompt**

**Note:** If no value or default is provided for a required variable, Terraform prompts us to enter a value when running interactively.

