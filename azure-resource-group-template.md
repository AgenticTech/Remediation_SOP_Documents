# Azure Resource Group Creation and Optimization

## Function: create_resource_group

### Input Parameters:
1. subscription_id: str
2. environment: str
3. region: str
4. sequence_number: int
5. tags: dict

### Conditions and Checks:
1. Validate environment:
   - Must be either 'prod' or 'nonprod'
2. Validate region:
   - Must be one of: 'South Central US', 'East US', 'West US2'
3. Validate tags:
   - Ensure all required tags are provided
4. Generate resource group name:
   - Format: f"Hcltest-{environment}-{region.replace(' ', '').lower()}-rg-{sequence_number:02d}"
5. Check if resource group already exists
6. Ensure Azure login is successful before creating resource group

### Naming Convention:
- Resource Group Name: Hcltest-<env>-<region>-rg-xx
  - <env>: 'prod' or 'nonprod'
  - <region>: 'southcentralus', 'eastus', or 'westus2'
  - xx: Two-digit sequential number (01, 02, etc.)

## Function: optimize_resource_groups

### Input Parameters:
1. subscription_id: str
2. days_threshold: int = 30

### Conditions and Checks:
1. List all resource groups in the subscription
2. For each resource group:
   - Check if it contains any resources
   - If empty, check its creation date
   - If empty for more than `days_threshold`, delete the resource group

### Notes:
- Implement proper error handling and logging
- Use Azure SDK for Python or Azure CLI for interacting with Azure
- Ensure proper authentication and authorization before performing actions
