# AWS EC2 Infrastructure Creation Standard Operating Procedure (SOP)

## Overview
This SOP details the process for creating an EC2 instance along with its supporting infrastructure (VPC, subnet, volumes) using Python.

## Platform
AWS

## Code Language
Python

## Required Dependencies
- boto3
- AWS SDK for Python

## Credentials Required
- AWS_ACCESS_KEY_ID
- AWS_SECRET_ACCESS_KEY
- AWS_REGION

## Input Parameters

### VPC Parameters
1. **vpc_name**
   - Type: string
   - Description: Name tag for the VPC
   - Required: true
   - Default: null

2. **vpc_cidr**
   - Type: string
   - Description: CIDR block for VPC
   - Required: true
   - Default: "10.0.0.0/16"
   - Validation: Must be valid CIDR notation

### Subnet Parameters
1. **subnet_name**
   - Type: string
   - Description: Name tag for the subnet
   - Required: true
   - Default: null

2. **subnet_cidr**
   - Type: string
   - Description: CIDR block for subnet
   - Required: true
   - Default: "10.0.1.0/24"
   - Validation: Must be within VPC CIDR range

### EC2 Instance Parameters
1. **instance_name**
   - Type: string
   - Description: Name tag for EC2 instance
   - Required: true
   - Default: null

2. **instance_type**
   - Type: string
   - Description: EC2 instance type
   - Required: true
   - Default: null
   - Example: "t2.micro", "t3.small"

3. **ami_id**
   - Type: string
   - Description: ID of the Amazon Machine Image
   - Required: true
   - Default: null

### Volume Parameters
1. **volume_size**
   - Type: integer
   - Description: Size of EBS volume in GB
   - Required: true
   - Default: 8

2. **volume_type**
   - Type: string
   - Description: Type of EBS volume
   - Required: false
   - Default: "gp3"
   - Options: "gp2", "gp3", "io1", "io2"

### General Parameters
1. **tags**
   - Type: dictionary
   - Description: Tags to apply to all resources
   - Required: false
   - Default: {}
   - Example: {"Environment": "Production", "Project": "WebApp"}

## Logic Flow

1. **VPC Creation Process**
   - Create VPC with specified CIDR
   - Enable DNS hostnames
   - Enable DNS support
   - Create and attach Internet Gateway
   - Configure main route table
   - Apply tags

2. **Subnet Creation Process**
   - Create subnet in VPC
   - Configure auto-assign public IP
   - Create custom route table
   - Create routes for internet access
   - Associate route table with subnet
   - Apply tags

3. **Security Group Setup**
   - Create security group in VPC
   - Configure inbound rules
   - Configure outbound rules
   - Apply tags

4. **EC2 Instance Creation**
   - Validate AMI availability
   - Create EC2 instance
   - Configure network settings
   - Attach security group
   - Apply tags

5. **Volume Management**
   - Create EBS volume
   - Attach volume to instance
   - Configure volume properties
   - Apply tags

## Error Handling Scenarios
- VPC CIDR overlap
- Subnet CIDR validation
- AMI not found
- Instance type not available
- Insufficient capacity
- Volume attachment failures
- Network configuration errors

## Success Criteria
- VPC and networking components created and configured
- EC2 instance running and accessible
- Volumes attached and available
- All resources properly tagged

## Monitoring Considerations
- Check instance status
- Monitor network connectivity
- Verify volume attachment status
- Validate security group rules

## Tags
- aws
- ec2
- vpc
- infrastructure
- compute
