# Azure Resource Group Creation Guide

## 1. Creation of Resource Group

### Prerequisites
1. Create Resource Group with the below Standard Naming.
2. Resource Group name should have the below naming convention:
   ```
   Hcltest-<env>-<region>-rg-xx
   ```
   Where:
   - Env = 'prod' or 'nonprod'
   - Region = 'South Central US' or 'East US' or 'West US2'
   - xx = Numerical numbers based on sequential order

### Notes
- Subscription, Region & Environment have to be provided by the user
- There should be Resource groups created only in South Central US or East US or West US2
- Resource groups creation should not complete unless tagging values are filled in

### Create Resource Group Using Below Steps

1. Login on Azure portal
2. Go to "Resource Group"
3. Click on "add"
4. Provide Resource group name
5. Select Subscription
6. Select location

Click **Create**

## Orphan Resource Groups - Optimization

Identify resource groups with no resources for more than 30 days and delete them.

## Reference

For Azure Resource Group creation:
[Microsoft Learn - Manage resource groups](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal)
