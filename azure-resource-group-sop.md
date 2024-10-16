# Standard Operating Procedure (SOP) for Azure Resource Group Creation and Management

## 1. Purpose
This SOP outlines the process for creating and managing Azure Resource Groups with reliability and consistency.

## 2. Scope
This procedure applies to all agents responsible for creating and managing Azure Resource Groups.

## 3. Prerequisites
- Access to Azure Portal
- Appropriate permissions to create Resource Groups
- Knowledge of the target subscription, environment, and region

## 4. Critical Note on User Inputs
**IMPORTANT:** Subscription, Region, and Environment MUST be provided by the user. These are critical inputs that determine the function parameters and overall behavior of the Resource Group management code. Never assume or hard-code these values.

## 5. Resource Group Naming Convention
Use the following format for Resource Group names:
```
Hcltest-<env>-<region>-rg-xx
```
Where:
- `<env>` is either 'prod' or 'nonprod' (user-provided)
- `<region>` is one of 'South Central US', 'East US', or 'West US2' (user-provided)
- `xx` is a two-digit sequential number (e.g., 01, 02, 03)

Example: `Hcltest-prod-eastus-rg-01`

## 6. Procedure for Creating a Resource Group

1. Log in to the Azure Portal (https://portal.azure.com)
2. Navigate to "Resource Groups"
3. Click on "Add" or "+ Create"
4. Fill in the required information:
   - Subscription: Select the appropriate subscription (user-provided)
   - Resource group name: Use the naming convention described in Section 5
   - Region: Select either 'South Central US', 'East US', or 'West US2' (user-provided)
5. Add required tags (Note: Resource group creation should not complete unless tagging values are filled in)
6. Review and create the Resource Group

## 7. Code Generation Guidelines for Resource Group Creation

When generating code to create Azure Resource Groups programmatically, follow these guidelines:

1. Use Azure CLI, Azure PowerShell, or Azure SDK for your preferred language.
2. Always design functions to accept Subscription, Region, and Environment as parameters. Never hard-code these values.
3. Implement strict input validation to ensure:
   - The subscription ID is valid
   - The environment is either 'prod' or 'nonprod'
   - The region is one of 'South Central US', 'East US', or 'West US2'
   - The name follows the correct format
4. Include comprehensive error handling and logging.
5. Implement a mechanism to generate the sequential number (xx) automatically.
6. Ensure that tagging is mandatory and validate that all required tags are present.

Example pseudo-code structure:

```python
def create_resource_group(subscription_id, environment, region, tags):
    # CRITICAL: subscription_id, environment, and region are user-provided
    # Validate inputs
    validate_inputs(subscription_id, environment, region, tags)
    
    # Generate name
    rg_name = generate_resource_group_name(environment, region)
    
    # Create resource group
    try:
        # Azure SDK code to create resource group
        # Ensure tags are applied
        print(f"Resource Group {rg_name} created successfully")
    except Exception as e:
        log_error(f"Failed to create Resource Group: {str(e)}")
        raise

def validate_inputs(subscription_id, environment, region, tags):
    if not is_valid_subscription(subscription_id):
        raise ValueError("Invalid subscription ID")
    if environment not in ['prod', 'nonprod']:
        raise ValueError("Environment must be 'prod' or 'nonprod'")
    if region not in ['South Central US', 'East US', 'West US2']:
        raise ValueError("Invalid region")
    if not tags:
        raise ValueError("Tags are required")
    # Add more validation as needed

def generate_resource_group_name(environment, region):
    # Implement name generation logic
    pass
```

## 8. Code Generation Guidelines for Managing Orphan Resource Groups

When generating code to manage orphan Resource Groups, follow these guidelines:

1. Use Azure SDK or Azure CLI for querying and managing Resource Groups.
2. Always require the subscription ID as a parameter, provided by the user.
3. Implement a function to check if a Resource Group is empty.
4. Use Azure's API to get the creation date of the Resource Group.
5. Implement logic to calculate the age of empty Resource Groups.
6. Create a report generation function for flagged Resource Groups.
7. Implement a safe deletion mechanism with proper error handling.

Example pseudo-code structure:

```python
from datetime import datetime, timedelta

def manage_orphan_resource_groups(subscription_id, age_threshold_days=30):
    # CRITICAL: subscription_id is user-provided
    if not is_valid_subscription(subscription_id):
        raise ValueError("Invalid subscription ID")

    # Get all resource groups
    resource_groups = get_all_resource_groups(subscription_id)
    
    orphan_rgs = []
    for rg in resource_groups:
        if is_resource_group_empty(rg):
            creation_date = get_resource_group_creation_date(rg)
            if (datetime.now() - creation_date).days > age_threshold_days:
                orphan_rgs.append(rg)
    
    # Generate report
    generate_orphan_rg_report(orphan_rgs)
    
    # Delete orphan RGs after approval
    if get_approval_for_deletion(orphan_rgs):
        for rg in orphan_rgs:
            try:
                delete_resource_group(rg)
                print(f"Deleted orphan Resource Group: {rg.name}")
            except Exception as e:
                log_error(f"Failed to delete Resource Group {rg.name}: {str(e)}")

def is_valid_subscription(subscription_id):
    # Implement logic to validate subscription ID
    pass

def is_resource_group_empty(resource_group):
    # Implement logic to check if RG is empty
    pass

def get_resource_group_creation_date(resource_group):
    # Implement logic to get RG creation date
    pass

def generate_orphan_rg_report(orphan_rgs):
    # Implement report generation logic
    pass

def get_approval_for_deletion(orphan_rgs):
    # Implement approval mechanism
    pass

def delete_resource_group(resource_group):
    # Implement safe deletion logic
    pass
```

## 9. Best Practices

1. Always use the specified naming convention.
2. Only create Resource Groups in the approved regions.
3. Ensure all required tags are applied during creation.

## 10. References

- [Azure Resource Manager documentation](https://learn.microsoft.com/en-us/azure/azure-resource-manager/)
- [Manage Azure Resource Groups](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal)

## 11. Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2024-10-16 | AI Assistant | Initial version |
| 1.1 | 2024-10-16 | AI Assistant | Added code generation guidelines for managing orphan Resource Groups; Updated Best Practices section |
| 1.2 | 2024-10-16 | AI Assistant | Added emphasis on user-provided inputs; Updated code examples to reflect this critical point |

