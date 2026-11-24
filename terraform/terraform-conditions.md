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