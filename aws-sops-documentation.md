# AWS Standard Operating Procedures (SOPs) Documentation

## 1. Resolving High CPU Utilization

### Description
This procedure outlines the steps to resolve high CPU utilization in an AWS EC2 instance using Boto3.

### Explanation
High CPU utilization can be caused by various factors such as inefficient code, insufficient instance size, or resource-intensive processes. This procedure involves identifying the affected instance, analyzing its metrics, and taking appropriate action such as scaling up the instance or enabling auto-scaling.

### Python Code (using Boto3)

```python
import boto3
import time

def resolve_high_cpu_utilization(instance_id, region_name='us-west-2', cpu_threshold=70, scale_up_type='t3.medium'):
    # Initialize Boto3 clients
    ec2_client = boto3.client('ec2', region_name=region_name)
    cloudwatch_client = boto3.client('cloudwatch', region_name=region_name)

    # Get current instance details
    instance_info = ec2_client.describe_instances(InstanceIds=[instance_id])['Reservations'][0]['Instances'][0]
    current_instance_type = instance_info['InstanceType']

    # Check CPU utilization
    response = cloudwatch_client.get_metric_statistics(
        Namespace='AWS/EC2',
        MetricName='CPUUtilization',
        Dimensions=[{'Name': 'InstanceId', 'Value': instance_id}],
        StartTime=time.time() - 3600,  # Last hour
        EndTime=time.time(),
        Period=300,  # 5-minute intervals
        Statistics=['Average']
    )

    # Calculate average CPU utilization
    datapoints = response['Datapoints']
    if datapoints:
        avg_cpu = sum(point['Average'] for point in datapoints) / len(datapoints)
        print(f"Average CPU utilization: {avg_cpu:.2f}%")

        if avg_cpu > cpu_threshold:
            print(f"CPU utilization is above threshold. Current instance type: {current_instance_type}")
            
            # Stop the instance
            ec2_client.stop_instances(InstanceIds=[instance_id])
            waiter = ec2_client.get_waiter('instance_stopped')
            waiter.wait(InstanceIds=[instance_id])
            
            # Modify instance type
            ec2_client.modify_instance_attribute(
                InstanceId=instance_id,
                Attribute='instanceType',
                Value=scale_up_type
            )
            
            # Start the instance
            ec2_client.start_instances(InstanceIds=[instance_id])
            print(f"Instance scaled up to {scale_up_type}")
        else:
            print("CPU utilization is within acceptable range. No action needed.")
    else:
        print("No CPU utilization data available.")

# Usage example
resolve_high_cpu_utilization('i-1234567890abcdef0')
```

## 2. Resolving High Disk Utilization

### Description
This procedure outlines the steps to resolve high disk utilization in an AWS EC2 instance using Boto3.

### Explanation
High disk utilization can lead to performance issues and potential data loss. This procedure involves identifying the affected instance, analyzing its disk usage, and taking appropriate action such as expanding the EBS volume or attaching a new volume.

### Python Code (using Boto3)

```python
import boto3
import time

def resolve_high_disk_utilization(instance_id, volume_id, region_name='us-west-2', disk_threshold=80, increase_size_gb=20):
    # Initialize Boto3 clients
    ec2_client = boto3.client('ec2', region_name=region_name)
    cloudwatch_client = boto3.client('cloudwatch', region_name=region_name)

    # Check disk utilization
    response = cloudwatch_client.get_metric_statistics(
        Namespace='AWS/EC2',
        MetricName='DiskSpaceUtilization',
        Dimensions=[
            {'Name': 'InstanceId', 'Value': instance_id},
            {'Name': 'Filesystem', 'Value': '/dev/xvda1'},
            {'Name': 'MountPath', 'Value': '/'}
        ],
        StartTime=time.time() - 3600,  # Last hour
        EndTime=time.time(),
        Period=300,  # 5-minute intervals
        Statistics=['Average']
    )

    # Calculate average disk utilization
    datapoints = response['Datapoints']
    if datapoints:
        avg_disk = sum(point['Average'] for point in datapoints) / len(datapoints)
        print(f"Average disk utilization: {avg_disk:.2f}%")

        if avg_disk > disk_threshold:
            print(f"Disk utilization is above threshold. Increasing volume size.")
            
            # Describe the volume to get its current size
            volume_info = ec2_client.describe_volumes(VolumeIds=[volume_id])['Volumes'][0]
            current_size = volume_info['Size']
            new_size = current_size + increase_size_gb

            # Modify the volume size
            ec2_client.modify_volume(
                VolumeId=volume_id,
                Size=new_size
            )
            
            print(f"Volume size increased from {current_size}GB to {new_size}GB")
            print("Note: You may need to extend the file system on the instance after the volume modification is complete.")
        else:
            print("Disk utilization is within acceptable range. No action needed.")
    else:
        print("No disk utilization data available.")

# Usage example
resolve_high_disk_utilization('i-1234567890abcdef0', 'vol-1234567890abcdef0')
```

## 3. Resolving High Network Latency in AWS Server by Implementing CDN

### Description
This procedure outlines the steps to resolve high network latency in an AWS server by implementing Amazon CloudFront as a Content Delivery Network (CDN) using Boto3.

### Explanation
High network latency can significantly impact user experience. Implementing a CDN can help reduce latency by caching content at edge locations closer to users. This procedure involves creating a CloudFront distribution for an existing S3 bucket or EC2 instance.

### Python Code (using Boto3)

```python
import boto3

def implement_cdn(origin_domain_name, region_name='us-west-2'):
    # Initialize Boto3 client
    cloudfront_client = boto3.client('cloudfront', region_name=region_name)

    # Create a CloudFront distribution
    response = cloudfront_client.create_distribution(
        DistributionConfig={
            'CallerReference': 'my-distribution-' + str(int(time.time())),
            'DefaultRootObject': 'index.html',
            'Origins': {
                'Quantity': 1,
                'Items': [
                    {
                        'Id': 'myOrigin',
                        'DomainName': origin_domain_name,
                        'S3OriginConfig': {
                            'OriginAccessIdentity': ''
                        },
                    },
                ]
            },
            'DefaultCacheBehavior': {
                'TargetOriginId': 'myOrigin',
                'ViewerProtocolPolicy': 'redirect-to-https',
                'MinTTL': 0,
                'AllowedMethods': {
                    'Quantity': 2,
                    'Items': ['GET', 'HEAD'],
                    'CachedMethods': {
                        'Quantity': 2,
                        'Items': ['GET', 'HEAD'],
                    }
                },
                'ForwardedValues': {
                    'QueryString': False,
                    'Cookies': {'Forward': 'none'},
                },
            },
            'Enabled': True,
        }
    )

    distribution_id = response['Distribution']['Id']
    distribution_domain = response['Distribution']['DomainName']
    print(f"CloudFront distribution created with ID: {distribution_id}")
    print(f"Distribution domain: {distribution_domain}")
    print("Please update your DNS records to point to this CloudFront distribution.")

# Usage example
implement_cdn('my-origin-bucket.s3.amazonaws.com')
```

## 4. Resolving AWS S3 Bucket Access Denied

### Description
This procedure outlines the steps to resolve "Access Denied" errors when attempting to access an AWS S3 bucket using Boto3.

### Explanation
S3 bucket access denied errors can occur due to various reasons such as insufficient IAM permissions, bucket policies, or incorrect credentials. This procedure involves checking and updating the necessary permissions to grant access to the S3 bucket.

### Python Code (using Boto3)

```python
import boto3
from botocore.exceptions import ClientError

def resolve_s3_access_denied(bucket_name, user_arn, region_name='us-west-2'):
    # Initialize Boto3 clients
    s3_client = boto3.client('s3', region_name=region_name)
    iam_client = boto3.client('iam', region_name=region_name)

    try:
        # Try to access the bucket
        s3_client.head_bucket(Bucket=bucket_name)
        print(f"Access to bucket '{bucket_name}' is already granted.")
    except ClientError as e:
        error_code = e.response['Error']['Code']
        if error_code == '403':
            print(f"Access denied to bucket '{bucket_name}'. Attempting to resolve...")

            # Update bucket policy
            bucket_policy = {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Sid": "AllowUserAccess",
                        "Effect": "Allow",
                        "Principal": {"AWS": user_arn},
                        "Action": ["s3:GetObject", "s3:ListBucket"],
                        "Resource": [
                            f"arn:aws:s3:::{bucket_name}",
                            f"arn:aws:s3:::{bucket_name}/*"
                        ]
                    }
                ]
            }

            s3_client.put_bucket_policy(Bucket=bucket_name, Policy=str(bucket_policy))
            print(f"Bucket policy updated for '{bucket_name}'")

            # Update IAM user policy
            user_policy = {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Allow",
                        "Action": [
                            "s3:GetObject",
                            "s3:ListBucket"
                        ],
                        "Resource": [
                            f"arn:aws:s3:::{bucket_name}",
                            f"arn:aws:s3:::{bucket_name}/*"
                        ]
                    }
                ]
            }

            username = user_arn.split('/')[-1]
            iam_client.put_user_policy(
                UserName=username,
                PolicyName=f"{bucket_name}-access-policy",
                PolicyDocument=str(user_policy)
            )
            print(f"IAM user policy updated for user '{username}'")

            print("Access should now be granted. Please try accessing the bucket again.")
        else:
            print(f"An error occurred: {e}")

# Usage example
resolve_s3_access_denied('my-bucket', 'arn:aws:iam::123456789012:user/myuser')
```

This documentation provides standard procedures for resolving common AWS issues using Boto3. Each procedure includes a description, explanation, and Python code that can be used by AI agents to dynamically create solutions without errors.
