# Virtual Machine Deployment and Patching Guide

## Prerequisites

### Virtual Machine Naming Convention

Create Virtual machines with the following standard naming convention:

```
Hcltest-<os>-<env>-<region>-vm-xx
```

Where:
- `OS` = 'win' or 'lin'
- `Env` = 'prod' or 'nonprod'
- `Region` = 'South Central US' or 'East US' or 'West US2'
- `xx` = Numerical numbers based on sequential order

### Important Notes

- OS, OS version, Subscription, Region, Environment, virtual network, and subnet must be provided by the user.
- Virtual machines should only be created in South Central US, East US, or West US2 regions.

### Reference
For Azure Virtual machine creation, refer to:
[Microsoft Learn - Create a Windows VM in the Azure portal](https://learn.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal)

## Patching Azure Windows Server

1. In the Azure Update manager, select the Windows VM to be patched or updated.
2. Add a schedule to the resource group where the Windows VM is hosted.
3. Set schedule frequency to "Monthly".
4. Choose a maintenance window between 6 PM CDT on every 15th day of the month.
5. Configure to "Always Reboot" after updates or patching.

### Reference
For Azure Windows VM patching, refer to:
[Microsoft Learn - Prerequisites for scheduled patching](https://learn.microsoft.com/en-us/azure/update-manager/prerequsite-for-schedule-patching?tabs=new-prereq-portal%2Cauto-portal)
