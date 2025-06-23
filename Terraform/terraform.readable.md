# Terraform Interview Guide: Key Concepts, Best Practices, and Examples

---

## 1. Infrastructure as Code Fundamentals

### Terraform Workflow
- **init**: Initializes the working directory and downloads provider plugins.
- **plan**: Shows the execution plan and what changes will be made.
- **apply**: Applies the planned changes to reach the desired state.

```shell
# Typical workflow
terraform init
terraform plan -out=tfplan
terraform apply tfplan
```

### State Management
- Terraform keeps track of resources in a state file (`terraform.tfstate`).
- State is critical for mapping real resources to configuration.

### Provider Configuration
- Providers are plugins for cloud platforms (AWS, Azure, GCP).

```hcl
provider "azurerm" {
  features {}
}
```

### Resource Dependencies
- Terraform automatically infers dependencies, but `depends_on` can be used for explicit dependencies.

```hcl
resource "azurerm_resource_group" "rg" {
  name     = "example-rg"
  location = "westeurope"
}

resource "azurerm_storage_account" "sa" {
  name                     = "examplestoracc"
  resource_group_name      = azurerm_resource_group.rg.name
  location                 = azurerm_resource_group.rg.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
  depends_on               = [azurerm_resource_group.rg]
}
```

---

## 2. Resource Management

### Creating, Modifying, Destroying Resources
- Use `terraform apply` to create/modify, `terraform destroy` to delete resources.

### Resource Blocks and Syntax
```hcl
resource "azurerm_virtual_network" "vnet" {
  name                = "myVnet"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}
```

### Data Sources vs Resources
- **Resource**: Creates and manages infrastructure.
- **Data Source**: Reads information from existing infrastructure.

```hcl
data "azurerm_client_config" "current" {}
```

### Meta-Arguments
- **count**: Create multiple resources.
- **for_each**: Iterate over maps/sets.
- **depends_on**: Explicit dependencies.

```hcl
resource "azurerm_network_interface" "nic" {
  count = 2
  ...
}

resource "azurerm_subnet" "subnet" {
  for_each = var.subnets
  ...
}
```

---

## 3. Variables and Outputs

### Variable Types and Declarations
```hcl
variable "location" {
  type    = string
  default = "westeurope"
}
```

### Local Variables
```hcl
locals {
  tags = {
    environment = var.environment
    owner       = var.owner
  }
}
```

### Output Values
```hcl
output "resource_group_name" {
  value = azurerm_resource_group.rg.name
}
```

### Variable Precedence
- Environment variables > CLI flags > `terraform.tfvars` > default values.

---

## 4. State Management

### Remote State Storage
- Use remote backends (Azure Storage, S3, GCS) for team collaboration.

```hcl
terraform {
  backend "azurerm" {
    resource_group_name  = "tfstate-rg"
    storage_account_name = "tfstateaccount"
    container_name       = "tfstate"
    key                  = "terraform.tfstate"
  }
}
```

### State Locking
- Prevents concurrent changes (supported by most remote backends).

### Workspace Management
- Use workspaces for environment separation (dev, prod).

```shell
terraform workspace new dev
terraform workspace select prod
```

### Import Existing Resources
```shell
terraform import azurerm_resource_group.rg /subscriptions/0000/resourceGroups/my-rg
```

---

## 5. Modules

### Module Structure
- Modules are reusable Terraform configurations.

```
modules/
  vnet/
    main.tf
    variables.tf
    outputs.tf
```

### Input/Output Variables
```hcl
module "vnet" {
  source   = "./modules/vnet"
  vnet_name = "myVnet"
}
```

### Module Versioning
- Use versioned modules from the Terraform Registry.

```hcl
module "vnet" {
  source  = "Azure/vnet/azurerm"
  version = "3.5.0"
}
```

### Reusable Module Patterns
- Use modules for networking, compute, security, etc.

---

## 6. Advanced Concepts

### Provisioners
- Run scripts on resources after creation (use sparingly).

```hcl
resource "azurerm_virtual_machine" "vm" {
  ...
  provisioner "local-exec" {
    command = "echo VM created!"
  }
}
```

### Null Resources
- Use for orchestration or when no real resource is needed.

```hcl
resource "null_resource" "example" {
  provisioner "local-exec" {
    command = "echo Hello World"
  }
}
```

### Dynamic Blocks
- Generate nested blocks dynamically.

```hcl
resource "azurerm_network_security_group" "nsg" {
  dynamic "security_rule" {
    for_each = var.rules
    content {
      name                       = security_rule.value.name
      priority                   = security_rule.value.priority
      ...
    }
  }
}
```

### Provider Configuration
- Multiple provider instances for multi-region/multi-account deployments.

```hcl
provider "azurerm" {
  alias   = "eastus"
  features {}
  location = "eastus"
}
```

---

## 7. Best Practices

- **Code Organization:** Use modules, separate environments, and clear directory structure.
- **Security:** Never commit secrets; use Key Vault/Secrets Manager and remote state with encryption.
- **Tagging:** Enforce tagging for cost and resource management.
- **State File Management:** Use remote backends, enable state locking, and restrict access.

---

## Additional Topics

### Troubleshooting Scenarios
- Use `terraform plan` and `terraform state list` to debug issues.
- Check for drift with `terraform plan` and reconcile manually if needed.

### Performance Optimization
- Use `for_each` over `count` for complex objects.
- Limit resource changes in a single apply.

### Collaboration Workflows
- Use remote state, version control, and code reviews.
- Integrate with CI/CD (e.g., Azure DevOps, GitHub Actions) for automated plans/applies.

### CI/CD Integration Example (Azure DevOps YAML)
```yaml
- task: TerraformInstaller@0
  inputs:
    terraformVersion: '1.5.0'
- script: |
    terraform init
    terraform plan
    terraform apply -auto-approve
  displayName: 'Terraform Apply'
```

### Migration Strategies
- Import existing resources, refactor into modules, and gradually adopt IaC.

### Cost Optimization
- Use data sources to find existing resources.
- Destroy unused resources and use variables for sizing.

---

## Null Resource, Taint, and Provisioner in Terraform

### Null Resource
A `null_resource` is a resource that does not manage any real infrastructure object. It is useful for running provisioners or as a placeholder for orchestration logic.

**Example:**
```hcl
resource "null_resource" "example" {
  provisioner "local-exec" {
    command = "echo Hello from null_resource!"
  }
}
```

### Taint
`terraform taint` marks a resource as tainted, forcing it to be destroyed and recreated on the next `terraform apply`. This is useful when a resource is in a bad state or needs to be refreshed.

**Example:**
```powershell
terraform taint null_resource.example
terraform apply
```
This will re-run the provisioner in the `null_resource` above.

### Provisioner
Provisioners allow you to execute scripts or commands on a local or remote machine as part of the resource lifecycle. Use them sparingly, as they can make deployments less predictable.

**Example:**
```hcl
resource "azurerm_virtual_machine" "vm" {
  # ...existing code...
  provisioner "remote-exec" {
    inline = [
      "sudo apt-get update",
      "sudo apt-get install -y nginx"
    ]
  }
}
```

**Best Practice:**
- Prefer using provisioners only for bootstrapping or when configuration management tools (like Ansible) are not available.
- Use `null_resource` for orchestration or when you need to run scripts without creating real resources.
- Use `terraform taint` to force recreation of resources when needed.

---

*Let me know if you want more detailed examples for AWS, Azure, or GCP!*
