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
