# Terraform Scenario-Based Interview Questions and Answers

---

## 1. How do you manage sensitive data (like secrets or passwords) in Terraform?
**Answer:**
- Use environment variables or encrypted files to pass sensitive values.
- Store secrets in secure vaults (e.g., Azure Key Vault, AWS Secrets Manager) and use data sources to retrieve them at runtime.
- Avoid hardcoding secrets in `.tf` files or state files. Use `terraform state` commands to remove sensitive outputs if needed.
- Use `terraform-provider-vault` for HashiCorp Vault integration.
- Set `sensitive = true` on output variables to prevent them from being displayed in CLI output.

---

## 2. How do you handle resource dependencies in Terraform?
**Answer:**
- Terraform automatically infers dependencies based on resource references (e.g., using `resource.id` in another resource).
- For explicit dependencies, use the `depends_on` argument to ensure correct resource creation order.
- Example:
  ```hcl
  resource "aws_instance" "web" {
    depends_on = [aws_security_group.web_sg]
    # ...
  }
  ```

---

## 3. What is remote state, and why is it important?
**Answer:**
- Remote state stores Terraform state files in a shared backend (e.g., AWS S3, Azure Blob Storage, Terraform Cloud).
- It enables collaboration, locking, and recovery in team environments.
- Prevents local state file loss and supports state versioning.
- Example backend configuration:
  ```hcl
  terraform {
    backend "azurerm" {
      storage_account_name = "tfstateaccount"
      container_name       = "tfstate"
      key                  = "prod.terraform.tfstate"
    }
  }
  ```

---

## 4. How do you implement Infrastructure as Code (IaC) modularity in Terraform?
**Answer:**
- Use modules to encapsulate reusable infrastructure components.
- Store modules in local directories or remote registries (Terraform Registry, GitHub).
- Example usage:
  ```hcl
  module "network" {
    source = "./modules/network"
    vnet_name = "my-vnet"
    # ...
  }
  ```
- Use input variables and outputs for module parameterization and data sharing.

---

## 5. How do you manage multiple environments (dev, test, prod) in Terraform?
**Answer:**
- Use workspaces (`terraform workspace new dev`) to separate state files per environment.
- Parameterize environment-specific values using variable files (e.g., `dev.tfvars`, `prod.tfvars`).
- Use folder structure or modules to organize environment resources.
- Example command:
  ```sh
  terraform apply -var-file="prod.tfvars"
  ```

---

## 6. How do you handle resource drift in Terraform?
**Answer:**
- **Resource drift** occurs when the real infrastructure changes outside of Terraform (e.g., manual edits in the cloud console), causing a mismatch between the actual state and Terraform’s state file.

### Step-by-Step Example: Detecting and Reconciling Drift
1. **Detect Drift:**
   - Run `terraform plan`. Terraform compares the current state file with the actual infrastructure and shows any differences (drift).
   - Example: If someone manually changes the VM size in Azure Portal, `terraform plan` will show that the VM size in the state file does not match the real VM size.

2. **Refresh State:**
   - Run `terraform refresh` to update the state file with the real resource values from the cloud provider.
   - This helps Terraform recognize the current state before planning further changes.

3. **Reconcile Drift:**
   - Decide whether to keep the manual change or revert to the desired configuration in your `.tf` files.
   - To revert manual changes: Update your `.tf` files to the intended configuration and run `terraform apply` to bring resources back in line.
   - To accept manual changes: Update your `.tf` files to match the new configuration, then run `terraform apply` to update the state file.
   - Example: If a VM’s tag was manually changed, either update the `.tf` file to match the new tag or re-apply the original tag using `terraform apply`.

4. **Best Practices:**
   - Limit manual changes in the cloud console; always use Terraform for infrastructure updates.
   - Use remote state backends with locking to prevent concurrent changes.
   - Enable monitoring and alerts for critical resources (e.g., Azure Policy, AWS Config) to detect drift early.
   - Regularly run `terraform plan` and `terraform refresh` to catch drift before it causes issues.
   - Document and communicate any manual changes to the team.

---

## 7. How do you use Terraform with CI/CD pipelines?
**Answer:**
- Integrate Terraform commands (`init`, `plan`, `apply`) into pipeline stages (e.g., Azure DevOps, GitHub Actions).
- Store state remotely and use service principals or IAM roles for authentication.
- Use automated plan review and approval workflows.
- Example GitHub Actions step:
  ```yaml
  - name: Terraform Apply
    run: terraform apply -auto-approve
  ```

---

## 8. How do you manage provider versions and module versions in Terraform?
**Answer:**
- Specify provider versions in the `required_providers` block.
- Pin module versions using the `version` argument.
- Example:
  ```hcl
  terraform {
    required_providers {
      azurerm = {
        source  = "hashicorp/azurerm"
        version = ">= 3.0.0"
      }
    }
  }
  module "network" {
    source  = "terraform-azure-modules/network/azurerm"
    version = "3.5.0"
  }
  ```

---

## 9. How do you destroy only specific resources in Terraform?
**Answer:**
- Use `terraform destroy -target=resource_type.resource_name` to destroy a specific resource.
- Example:
  ```sh
  terraform destroy -target=azurerm_virtual_machine.vm1
  ```
- Use with caution to avoid dependency issues.

---

## 10. How do you handle secrets in Terraform outputs?
**Answer:**
- Mark outputs as sensitive (`sensitive = true`) to prevent them from being displayed in CLI output or logs.
- Avoid exposing secrets in outputs unless absolutely necessary.
- Example:
  ```hcl
  output "db_password" {
    value     = azurerm_key_vault_secret.db_password.value
    sensitive = true
  }
  ```

---

## 11. How do you roll back changes in Terraform?
**Answer:**
- Terraform does not natively support rollback; you must manually revert `.tf` files and re-apply.
- Use versioned remote state to restore previous state files if needed.
- For critical changes, use `terraform plan` and manual approval before applying.

---

## 12. How do you import existing resources into Terraform?
**Answer:**
- Use `terraform import` to bring existing resources under Terraform management.
- Example:
  ```sh
  terraform import azurerm_resource_group.rg1 /subscriptions/xxxx/resourceGroups/rg1
  ```
- Update `.tf` files to match the imported resource configuration.

---

## 13. How do you handle errors and troubleshooting in Terraform?
**Answer:**
- Review error messages and logs for details.
- Use `terraform plan` and `terraform apply` with `-debug` for verbose output.
- Check provider documentation and resource dependencies.
- Validate syntax with `terraform validate`.

---

## 14. How do you enforce compliance and security in Terraform deployments?
**Answer:**
- Use Sentinel or OPA (Open Policy Agent) for policy enforcement in Terraform Enterprise/Cloud.
- Integrate with cloud-native policy tools (e.g., Azure Policy, AWS Config).
- Use code reviews, automated scanning, and static analysis tools (e.g., tfsec, checkov).

---

## 15. How do you upgrade Terraform versions and providers safely?
**Answer:**
- Review upgrade guides and changelogs.
- Test upgrades in non-production environments first.
- Use `terraform init -upgrade` to update providers.
- Validate and plan before applying changes.

---

## 16. How do you handle resource tainting and state refreshing in Terraform?
**Answer:**
- `terraform refresh` is not a new feature; it has been part of Terraform for a long time. It updates the state file with the actual resource values from the provider, helping you detect drift and plan further changes.
- `terraform taint` is another useful command. It marks a resource as "tainted" in the state file, meaning Terraform will destroy and recreate it on the next `terraform apply`. Use this when you want to force recreation of a resource due to suspected issues or configuration changes that aren't detected as drift.

**Example:**
```sh
terraform taint azurerm_virtual_machine.vm1
terraform apply
```
This will destroy and recreate `vm1` even if no changes are detected in the configuration.

**Best Practice:**
- Use `terraform taint` for troubleshooting or when a resource is in a bad state and needs to be replaced.
- Use `terraform refresh` to keep your state file in sync with real infrastructure.

---

*Let me know if you want more scenario-based questions or printable checklists!*
