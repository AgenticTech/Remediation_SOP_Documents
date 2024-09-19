# Resolving High Disk Utilization - Standard Operating Procedure

## Explanation
High disk utilization can lead to performance issues and potential system failures. This procedure helps identify and resolve disk space problems.

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

4. **Get disk utilization metrics**
   ```python
   end_time = datetime.datetime.utcnow()
   start_time = end_time - datetime.timedelta(hours=1)
   
   response = cloudwatch_client.get_metric_statistics(
       Namespace='AWS/EC2',
       MetricName='DiskSpaceUtilization',
       Dimensions=[
           {'Name': 'InstanceId', 'Value': instance['InstanceId']},
           {'Name': 'Filesystem', 'Value': '/dev/xvda1'},
           {'Name': 'MountPath', 'Value': '/'}
       ],
       StartTime=start_time,
       EndTime=end_time,
       Period=300,
       Statistics=['Average']
   )
   ```

5. **Analyze disk usage**
   ```python
   if any(datapoint['Average'] > 80 for datapoint in response['Datapoints']):
       print("High disk usage detected. Taking corrective action.")
   ```

6. **Identify large files and directories**
   ```python
   response = ssm_client.send_command(
       InstanceIds=[instance['InstanceId']],
       DocumentName='AWS-RunShellScript',
       Parameters={'commands': ['du -sh /* | sort -rh | head -n 5']}
   )
   ```

7. **Take corrective action**
   ```python
   # Example: Move data to S3
   s3_client = boto3.client('s3')
   s3_client.upload_file('/path/to/large/file', 'my-bucket', 'large-file-backup')
   ```

8. **Expand EBS volume (if necessary)**
   ```python
   volume_id = instance['BlockDeviceMappings'][0]['Ebs']['VolumeId']
   ec2_client.modify_volume(VolumeId=volume_id, Size=100)
   ```

9. **Extend file system**
   ```python
   ssm_client.send_command(
       InstanceIds=[instance['InstanceId']],
       DocumentName='AWS-RunShellScript',
       Parameters={'commands': ['sudo growpart /dev/xvda 1', 'sudo resize2fs /dev/xvda1']}
   )
   ```

10. **Monitor and verify**
    ```python
    # Continue monitoring disk utilization using CloudWatch metrics
    ```

## Note
This SOP provides a template for resolving high disk utilization issues. Ensure to replace placeholder values (e.g., instance IDs, file paths, bucket names) with actual values from your AWS environment. Always test in a non-production environment before applying changes to critical systems.
