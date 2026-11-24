# Terraform Loops

Loops are used to create multiple resources without writing the same code again and again.

In Terraform, we use `count` and `for_each` to create multiple instances of a resource.

## Count-Based Loop

`count` is a meta-argument used to create multiple resources by looping through each item in a list.

- We can use `count` with a number to specify how many resources to create.
- We can use `count` with a list to loop through each item using its index (`count.index`).
- Terraform provides a special variable called `count.index` to identify the current resource index.

### Example

```hcl
resource "aws_instance" "web" {
  count         = 3
  ami           = "ami-123456"
  instance_type = "t3.micro"

  tags = {
    Name = "web-${count.index}"
  }
}
```

### Output

Terraform creates 3 EC2 instances:

| Resource | Name |
|---|---|
| `aws_instance.web[0]` | web-0 |
| `aws_instance.web[1]` | web-1 |
| `aws_instance.web[2]` | web-2 |

### Explanation

- `count = 3` → Creates 3 EC2 instances.
- `count.index` → Gives the index number of each resource, starting from 0.
- `count.index` automatically increases for each resource (0, 1, 2).
- Resource names will be `web-0`, `web-1`, and `web-2`.

---

## Interpolation in Terraform

Interpolation in Terraform means inserting a variable or expression inside a string.

Terraform uses `${}` syntax for interpolation.

### Example

```hcl
# Define an environment variable
variable "environment" {
  default = "dev"
}

# Create 2 EC2 instances
resource "aws_instance" "web" {
  count = 2

  # Assign a unique name to each EC2 instance
  tags = {
    Name = "web-${var.environment}-${count.index}"
  }
}
```

### Output

Terraform generates the following EC2 instance names:

```text
web-dev-0
web-dev-1
```

### Explanation

- `${var.environment}` → Inserts the variable value (`dev`).
- `${count.index}` → Inserts the current resource index number (0, 1).

### Remember

- `count` → Specifies how many resources Terraform should create.
- `count.index` → Gives the index number of each resource.
- `${}` → Used to insert variables or expressions inside strings.

---

## Using count with a List

We can use `count` with a list to create resources based on the number of items in that list.

### Example

```hcl
variable "instances" {
  default = ["mongodb", "redis", "mysql"]
}

resource "aws_instance" "web" {
  count         = length(var.instances)
  ami           = "ami-123456"
  instance_type = "t3.micro"

  tags = {
    Name = var.instances[count.index]
  }
}
```

### Explanation

- `var.instances` → Contains a list of instance names.
- `length(var.instances)` → Returns the number of items in the list (3).
- `count` → Creates 3 EC2 instances.
- `count.index` → Gets the current index (0, 1, 2).
- `var.instances[count.index]` → Gets the corresponding item from the list.

### Output

| Resource | Name |
|---|---|
| `aws_instance.web[0]` | mongodb |
| `aws_instance.web[1]` | redis |
| `aws_instance.web[2]` | mysql |

**Remember:**

- List indexing starts from `0`.
- `count.index` selects the corresponding list item.
- `length()` returns the total number of items in a list.


# Terraform for_each Loop

`for_each` is a meta-argument used to create multiple resources by looping through each item in a map 

- We can use `for_each` with a map or set of strings.
- Terraform provides a special variable called `each` to identify the current item.
- `each.key` → Gives the current key.
- `each.value` → Gives the current value.

## Example: for_each Loop

```hcl
variable "instances" {
  default = {
    mongodb = "t3.micro"
    redis   = "t3.small"
    mysql   = "t3.micro"
  }
}

resource "aws_instance" "web" {
  for_each      = var.instances
  ami           = "ami-123456"
  instance_type = each.value

  tags = {
    Name = each.key
  }
}
```

## Output

Terraform creates 3 EC2 instances:

| Resource | Instance Type | Name |
|---|---|---|
| `aws_instance.web["mongodb"]` | t3.micro | mongodb |
| `aws_instance.web["redis"]` | t3.small | redis |
| `aws_instance.web["mysql"]` | t3.micro | mysql |

## Explanation

- `for_each` → Loops through each item in the map.
- `each.key` → Gets the current key (`mongodb`, `redis`, `mysql`).
- `each.value` → Gets the current value (`t3.micro`, `t3.small`, `t3.micro`).
- Each map item creates one EC2 instance.

