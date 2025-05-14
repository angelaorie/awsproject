# Automated Incident Response with AWS Lambda & S3

Automatically detect and respond to unauthorized S3 actions using AWS Lambda, CloudTrail, and EventBridge.

## Features
- 🚨 Real-time detection of unauthorized S3 API calls (`DeleteBucket`, `PutBucketPolicy`, etc.)
- ⚡ Automatic remediation (quarantine buckets, revoke IAM keys)
- 📧 Alerting via Amazon SNS (email/SMS)
- 🔍 Full CloudTrail integration for audit trails

## Architecture
```mermaid
graph TD
    A[CloudTrail] -->|Logs S3 API Calls| B(EventBridge)
    B -->|Triggers on AccessDenied| C[Lambda]
    C -->|Isolate Bucket| D[S3]
    C -->|Send Alerts| E[SNS]
    C -->|Revoke Keys| F[IAM]






## Prerequisites
- AWS account with admin permissions
- AWS CLI configured (`aws configure`)
- Python 3.9+ (for Lambda development)

## Setup

### 1. Create S3 Test Bucket
```bash
aws s3api create-bucket --bucket test-security-bucket-$(aws sts get-caller-identity --query Account --output text)
```

### 2. Deploy Lambda Function
1. Create new Lambda (Python 3.9) named `S3SecurityResponder`
2. Paste this code:
```python
import boto3
import json

def lambda_handler(event, context):
    s3 = boto3.client('s3')
    sns = boto3.client('sns')
    
    # Extract event details
    event_detail = event['detail']
    bucket_name = event_detail['requestParameters']['bucketName']
    user_arn = event_detail['userIdentity']['arn']
    
    # 1. Quarantine bucket (disable public access)
    s3.put_public_access_block(
        Bucket=bucket_name,
        PublicAccessBlockConfiguration={
            'BlockPublicAcls': True,
            'IgnorePublicAcls': True,
            'BlockPublicPolicy': True,
            'RestrictPublicBuckets': True
        }
    )
    
    # 2. Send alert
    sns.publish(
        TopicArn='arn:aws:sns:YOUR_REGION:YOUR_ACCOUNT_ID:SecurityAlerts',
        Message=f"🚨 Unauthorized S3 action detected on {bucket_name} by {user_arn}"
    )
    
    return {'statusCode': 200}
```

### 3. Configure EventBridge Rule
```json
{
  "source": ["aws.s3"],
  "detail-type": ["AWS API Call via CloudTrail"],
  "detail": {
    "eventSource": ["s3.amazonaws.com"],
    "eventName": ["DeleteBucket", "PutBucketPolicy"],
    "errorCode": ["AccessDenied"]
  }
}
```
Target: Your Lambda function

### 4. Set Up SNS Alerts
1. Create SNS topic `SecurityAlerts`
2. Subscribe your email/SMS

## Testing
```bash
# Simulate unauthorized action (will fail)
aws s3api delete-bucket --bucket test-security-bucket-YOUR_ACCOUNT_ID
```
Verify:
1. Lambda execution in CloudWatch Logs
2. SNS alert received
3. Bucket public access blocked (check S3 console)

## Permissions
Ensure Lambda execution role has:
- `AmazonS3FullAccess`
- `AmazonSNSFullAccess`

## Troubleshooting
| Issue | Solution |
|-------|----------|
| No Lambda trigger | Check EventBridge rule pattern matches CloudTrail events |
| SNS alerts missing | Verify Lambda has `sns:Publish` permissions |
| Bucket not quarantined | Check for `s3:PutPublicAccessBlock` permissions |

## License
MIT
```


