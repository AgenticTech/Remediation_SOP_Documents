# Resolving High CPU Utilization - Standard Operating Procedure

## Explanation
High CPU utilization can be caused by various factors, including resource-intensive applications, inefficient code, or inadequate instance sizing. This procedure outlines steps to identify the cause and take corrective action.

## Step-by-step procedure with code snippets

1. **Import required libraries**
   ```python
   import boto3
   import datetime
   ```

2. **Initialize AWS client**
   ```python
   ec2_client = boto3.client('ec2')
   cloudwatch_client = boto3.client('cloudwatch')
   ssm_client = boto3.client('ssm')
   ```

3. **Retrieve instance information**
   ```python
   response = ec2_client.describe_instances(InstanceIds=['i-1234567890abcdef0'])
   instance = response['Reservations'][0]['Instances'][0]
   ```

4. **Get CPU utilization metrics**
   ```python
   end_time = datetime.datetime.utcnow()
   start_time = end_time - datetime.timedelta(hours=1)
   
   response = cloudwatch_client.get_metric_statistics(
       Namespace='AWS/EC2',
       MetricName='CPUUtilization',
       Dimensions=[{'Name': 'InstanceId', 'Value': instance['InstanceId']}],
       StartTime=start_time,
       EndTime=end_time,
       Period=300,
       Statistics=['Average']
   )
   ```

5. **Analyze CPU usage**
   ```python
   if any(datapoint['Average'] > 80 for datapoint in response['Datapoints']):
       print("High CPU usage detected. Taking corrective action.")
   ```

6. **Identify resource-intensive processes**
   ```python
   response = ssm_client.send_command(
       InstanceIds=[instance['InstanceId']],
       DocumentName='AWS-RunShellScript',
       Parameters={'commands': ['ps aux --sort=-%cpu | head -n 5']}
   )
   ```

7. **Take corrective action**
   ```python
   # Example: Upgrade instance type
   ec2_client.modify_instance_attribute(
       InstanceId=instance['InstanceId'],
       InstanceType={'Value': 't3.large'}
   )
   ```

8. **Implement auto-scaling (optional)**
   ```python
   autoscaling_client = boto3.client('autoscaling')
   
   autoscaling_client.create_auto_scaling_group(
       AutoScalingGroupName='MyASG',
       LaunchTemplate={
           'LaunchTemplateId': 'lt-1234567890abcdef0',
           'Version': '$Latest'
       },
       MinSize=1,
       MaxSize=5,
       DesiredCapacity=2,
       VPCZoneIdentifier='subnet-1234567890abcdef0,subnet-0987654321fedcba0'
   )
   ```

9. **Monitor and verify**
   ```python
   # Continue monitoring CPU utilization using CloudWatch metrics
   ```

## Note
This SOP provides a template for resolving high CPU utilization issues. Ensure to replace placeholder values (e.g., instance IDs, subnet IDs) with actual values from your AWS environment. Always test in a non-production environment before applying changes to critical systems.
