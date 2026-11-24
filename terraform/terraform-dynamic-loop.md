# Dynamic Blocks in Terraform

## What is a Dynamic Block?

- A dynamic block is used to create multiple nested blocks inside a resource without writing the same block multiple times.
- It helps reduce duplicate code and makes Terraform configuration easier to maintain.

- Dynamic block uses a for_each loop to generate multiple nested blocks.

**Meaning of Nested Block:** A nested block is a block written inside another block.

For example, `ingress` is a nested block inside the `aws_security_group` resource.

---

## Without a Dynamic Block

Imagine you want to allow three ports in one Security Group:

- SSH – Port 22
- HTTP – Port 80
- HTTPS – Port 443

Normally, you need to write three separate `ingress` blocks manually.

**Example:**

```hcl
resource "aws_security_group" "demo" {
  name = "demo-sg"

  # Allow SSH traffic
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  # Allow HTTP traffic
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  # Allow HTTPS traffic
  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

Here, we manually write three ingress blocks with different port numbers.

---

## With a Dynamic Block

Instead of writing three separate `ingress` blocks, we can use one dynamic block.

Terraform automatically generates the required ingress blocks based on the input variable.

### 1. Input Variable

First, define the ingress rules using a map.

```hcl
variable "ingress_rules" {
  default = {
    ssh = {
      port        = 22
      cidr_blocks = ["0.0.0.0/0"]
    }

    http = {
      port        = 80
      cidr_blocks = ["0.0.0.0/0"]
    }

    https = {
      port        = 443
      cidr_blocks = ["0.0.0.0/0"]
    }
  }
}
```

The map contains three rules:

| Key | Port |
|---|---:|
| ssh | 22 |
| http | 80 |
| https | 443 |

### 2. Dynamic Block Example

```hcl
resource "aws_security_group" "dynamic" {
  name        = "dynamic-sg"
  description = "Security group using dynamic ingress rules"

  # Create ingress blocks dynamically using the ingress_rules map
  dynamic "ingress" {
    for_each = var.ingress_rules

    content {
      from_port   = ingress.value.port
      to_port     = ingress.value.port
      protocol    = "tcp"
      cidr_blocks = ingress.value.cidr_blocks
    }
  }
}
```

---

## How Dynamic Blocks Work in Terraform

1. **Input Variable:** Define the required rules using a list or map. Example: SSH (22), HTTP (80), and HTTPS (443).

2. **Dynamic Block:** Specify which nested block Terraform needs to create. Example: `dynamic "ingress"`.

3. **for_each:** Takes each rule from the input variable one by one.

4. **Special Iterator Variable:** Terraform automatically creates an iterator variable using the dynamic block name (`ingress`). It represents the current item's key and value during each iteration.

   - First iteration → SSH (22)
   - Second iteration → HTTP (80)
   - Third iteration → HTTPS (443)

5. **Content Block:** Defines what each generated ingress block should contain. Terraform takes values from the iterator variable and fills in the required arguments.

6. **Output:** Terraform generates three ingress blocks inside one Security Group resource.

---

**Important:** A dynamic block creates nested blocks, not separate resources. In this example, Terraform creates one Security Group containing three ingress rules.