
## GitHub Secrets Setup

Store the following in your repository:

| Secret Name              | Description                          |
|--------------------------|--------------------------------------|
| `AZURE_CLIENT_ID`        | Azure AD App client ID               |
| `AZURE_CLIENT_SECRET`    | Azure AD App client secret           |
| `AZURE_SUBSCRIPTION_ID`  | Azure subscription ID                |
| `AZURE_TENANT_ID`        | Azure tenant ID                      |
| `psql_admin_password`    | PostgreSQL admin password            |

##  How the GitHub Actions Workflow Works

1. **Triggers on push to `main`**
2. **Terraform setup** using `hashicorp/setup-terraform` GitHub Action
3. **Initializes Terraform**
4. **Runs `terraform plan`** with secrets passed as input variables
5. **Runs `terraform apply`** to deploy the DB
6. **Environment variables** (Azure credentials) are injected securely

> All sensitive data like DB password is pulled directly from GitHub Secrets, not written in the code.

## ⚠ Common Errors & Solutions

| ❌ Error | 🔍 Cause | 🛠️ Fix |
|---------|----------|--------|
| `ResourceGroupNotFound` | Resource group `psql-rg` doesn't exist | Create RG manually or add `azurerm_resource_group` in Terraform |
| `sku_name is not valid` | Wrong SKU format used | Use `B_Standard_B1ms` (for low-cost burstable tier) |
| `password_enabled not valid` | `authentication` block used (deprecated) | Remove `authentication` block from `main.tf` |
| `psql_admin_password not declared` | Variable mismatch | Ensure variable is named exactly `psql_admin_password` |
| `az login required` | Azure provider credentials not passed | Make sure all 4 credentials are set in GitHub Secrets and passed via `env:` |
| `client_id not declared` | Passing secrets via `-var` but variable not defined | Add corresponding variables in `variables.tf` |

##  Tips for Beginners

- **Never hardcode** passwords or secrets in `.tfvars`
- Terraform picks up secrets only when **variable names match exactly**
- Use **Terraform Cloud or Azure backend** for remote state in production

##  Final Output

Once deployment succeeds, you will get the **PostgreSQL FQDN** in the output, which can be used to connect your DB clients or apps.
