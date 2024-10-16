# Azure VM Patching Template

## Function: schedule_vm_patching

### Input Parameters
- vm_id: str (Azure resource ID of the VM)
- resource_group: str

### Conditions to Check and Implement
1. Validate input parameters:
   - VM exists and is accessible
   - VM is a Windows machine
2. Configure patching schedule:
   - Frequency: Monthly
   - Schedule: 15th day of the month at 6 PM CDT
   - Reboot: Always after updates

### Implementation Steps
1. Import necessary Azure SDK modules
2. Authenticate with Azure
3. Validate VM exists and is Windows
4. Create Update Management configuration
5. Apply patching schedule to the VM

### Error Handling
- Implement try-except blocks for Azure SDK calls
- Raise custom exceptions for invalid input parameters or non-Windows VMs

### Reference
VM Patching: https://learn.microsoft.com/en-us/azure/update-manager/prerequsite-for-schedule-patching?tabs=new-prereq-portal%2Cauto-portal
