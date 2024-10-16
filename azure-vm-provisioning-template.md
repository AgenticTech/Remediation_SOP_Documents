# Azure VM Provisioning Template

## Function: provision_azure_vm

### Input Parameters
- os: str ('win' or 'lin')
- env: str ('prod' or 'nonprod')
- region: str ('South Central US', 'East US', or 'West US2')
- os_version: str
- subscription: str
- virtual_network: str
- subnet: str

### Conditions to Check and Implement
1. Validate input parameters:
   - OS is 'win' or 'lin'
   - env is 'prod' or 'nonprod'
   - region is one of the three specified options
2. Generate VM name:
   - Format: f"Hcltest-{os}-{env}-{region.replace(' ', '').lower()}-vm-{xx}"
   - xx: sequential number (implement logic to determine)
3. Create VM using Azure SDK

### Naming Convention
- VM Name: Hcltest-<os>-<env>-<region>-vm-xx
  - os: 'win' or 'lin'
  - env: 'prod' or 'nonprod'
  - region: 'southcentralus', 'eastus', or 'westus2'
  - xx: sequential number

### Implementation Steps
1. Import necessary Azure SDK modules
2. Authenticate with Azure
3. Validate input parameters
4. Generate VM name
5. Create VM configuration
6. Provision VM
7. Return VM details

### Error Handling
- Implement try-except blocks for Azure SDK calls
- Raise custom exceptions for invalid input parameters

### Reference
VM Creation: https://learn.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal
