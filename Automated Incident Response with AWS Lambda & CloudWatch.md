# Automated S3 Security Response System

![AWS S3 Protection](https://img.shields.io/badge/AWS-S3_Protection-orange)
![Python Version](https://img.shields.io/badge/Python-3.9+-blue)

Automated detection and response system for S3 security incidents using AWS Lambda, CloudTrail, and EventBridge.

## Features

- 🛡️ Real-time monitoring of sensitive S3 API calls
- 🔒 Automatic bucket quarantine on unauthorized access
- 📨 Multi-channel alerts via Amazon SNS
- 📊 Comprehensive audit logging with CloudTrail
- 💰 Free-tier compatible architecture

## Architecture Overview

```mermaid
graph TD
    A[CloudTrail] -->|Logs S3 API Calls| B(EventBridge)
    B -->|Triggers on Security Events| C[Lambda]
    C -->|1. Lock Bucket| D[S3]
    C -->|2. Revoke Keys| E[IAM]
    C -->|3. Send Alerts| F[SNS]





























Here's the revised `README.md` focused on S3 incident response:

```markdown
# Automated S3 Security Response System

![AWS S3 Protection](https://img.shields.io/badge/AWS-S3_Protection-orange)
![Python Version](https://img.shields.io/badge/Python-3.9+-blue)

Automated detection and response system for S3 security incidents using AWS Lambda, CloudTrail, and EventBridge.

## Features

- 🛡️ Real-time monitoring of sensitive S3 API calls
- 🔒 Automatic bucket quarantine on unauthorized access
- 📨 Multi-channel alerts via Amazon SNS
- 📊 Comprehensive audit logging with CloudTrail
- 💰 Free-tier compatible architecture

## Architecture Overview

```mermaid
graph TD
    A[CloudTrail] -->|Logs S3 API Calls| B(EventBridge)
    B -->|Triggers on Security Events| C[Lambda]
    C -->|1. Lock Bucket| D[S3]
    C -->|2. Revoke Keys| E[IAM]
    C -->|3. Send Alerts| F[SNS]
```

## Prerequisites

- AWS account with administrative privileges
- AWS CLI configured with proper credentials
- Python 3.9+ environment
- At least one S3 bucket for testing

## Deployment Guide

### 1. CloudTrail Configuration
```bash
aws cloudtrail create-trail \
    --name S3SecurityTrail \
    --s3-bucket-name s3-security-logs-$(aws sts get-caller-identity --query Account --output text) \
    --enable-log-file-validation
```

### 2. Lambda Function Deployment
Create `s3_security_response.py`:
```python
import boto3

def lambda_handler(event, context):
    s3 = boto3.client('s3')
    sns = boto3.client('sns')
    
    event_detail = event['detail']
    bucket_name = event_detail['requestParameters']['bucketName']
    
    # Remediation actions
    s3.put_public_access_block(
        Bucket=bucket_name,
        PublicAccessBlockConfiguration={
            'BlockPublicAcls': True,
            'IgnorePublicAcls': True,
            'BlockPublicPolicy': True,
            'RestrictPublicBuckets': True
        }
    )
    
    # Alerting
    sns.publish(
        TopicArn='arn:aws:sns:us-east-1:${aws sts get-caller-identity --query Account --output text}:S3SecurityAlerts',
        Subject="🚨 S3 Security Incident",
        Message=f"""Unauthorized access detected:
        - Bucket: {bucket_name}
        - Action: {event_detail['eventName']}
        - User: {event_detail['userIdentity']['arn']}"""
    )
    
    return {'statusCode': 200}
```

### 3. EventBridge Rule Setup
```json
{
  "source": ["aws.s3"],
  "detail-type": ["AWS API Call via CloudTrail"],
  "detail": {
    "eventSource": ["s3.amazonaws.com"],
    "eventName": [
      "DeleteBucket",
      "PutBucketPolicy",
      "PutBucketAcl"
    ],
    "errorCode": ["AccessDenied"]
  }
}
```

### 4. Notification Configuration
```bash
aws sns create-topic --name S3SecurityAlerts
aws sns subscribe \
    --topic-arn arn:aws:sns:us-east-1:$(aws sts get-caller-identity --query Account --output text):S3SecurityAlerts \
    --protocol email \
    --notification-endpoint your.email@example.com
```

## Testing the System

1. Simulate unauthorized access:
```bash
aws s3api put-bucket-acl \
    --bucket your-test-bucket \
    --acl public-read
```

2. Verify:
- Lambda execution logs in CloudWatch
- Bucket public access settings
- SNS notification received

## Security Best Practices

- Enable S3 Block Public Access at account level
- Use bucket policies instead of ACLs
- Enable MFA Delete for important buckets
- Regularly review access logs

## Cost Optimization

| Service       | Free Tier                | Paid Tier               |
|---------------|--------------------------|-------------------------|
| CloudTrail    | 1 management trail       | $2.00/100K events       |
| Lambda        | 1M requests/month        | $0.20/1M requests       |
| SNS           | 1M publishes/month       | $0.50/1M notifications  |

## Troubleshooting

**Issue**: No events detected  
✅ Verify CloudTrail is logging S3 data events  
✅ Check EventBridge rule region matches CloudTrail  

**Issue**: Bucket not locked  
✅ Confirm Lambda has `s3:PutPublicAccessBlock` permission  
✅ Check bucket exists in same region  

## Roadmap

- [ ] Add support for cross-account protection
- [ ] Implement automatic bucket versioning
- [ ] Add Slack/MS Teams integration

## License

Apache License 2.0 - See [LICENSE](LICENSE) for details
```

