# Automated S3 Security Response System

![AWS S3 Protection](https://img.shields.io/badge/AWS-S3_Protection-orange)
![Python Version](https://img.shields.io/badge/Python-3.9+-blue)

Automated detection and response system for S3 security incidents using AWS Lambda, CloudTrail, and EventBridge.

## Features

- 🛡️ Real-time monitoring of sensitive S3 API calls
- 🔒 Automatic bucket quarantine on unauthorized access
- 📨 Multi-channel alerts via Amazon SNS
- 📊 Comprehensive audit logging with CloudTrail

## Setup Guide (AWS Console)

### 1. Configure CloudTrail Logging
1. Navigate to **AWS CloudTrail** service
2. Click **Create trail**
3. Enter trail name: `S3SecurityTrail`
4. Under **Storage location**, create a new S3 bucket or select existing
5. Enable **Log file validation**
6. Click **Create trail**

### 2. Create Lambda Function
1. Go to **AWS Lambda** service
2. Click **Create function**
3. Select **Author from scratch**
4. Enter name: `S3SecurityResponder`
5. Choose **Python 3.9** runtime
6. Under Permissions:
   - Select **Create a new role with basic Lambda permissions**
7. Click **Create function**
8. Paste the following code:

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
        TopicArn='arn:aws:sns:YOUR_REGION:YOUR_ACCOUNT_ID:S3SecurityAlerts',
        Subject="🚨 S3 Security Incident",
        Message=f"""Unauthorized access detected:
        - Bucket: {bucket_name}
        - Action: {event_detail['eventName']}
        - User: {event_detail['userIdentity']['arn']}"""
    )
    
    return {'statusCode': 200}

```

9. Click **Deploy**

### 3. Set Up EventBridge Rule
1. Navigate to **Amazon EventBridge** service
2. Click **Create rule**
3. Enter name: `S3SecurityMonitor`
4. For **Event bus**, select **default**
5. Under **Rule type**, select **Rule with an event pattern**
6. Click **Next**
7. For **Event source**, choose **AWS events or EventBridge partner events**
8. Paste this event pattern:

```json
{
  "source": ["aws.s3"],
  "detail-type": ["AWS API Call via CloudTrail"],
  "detail": {
    "eventSource": ["s3.amazonaws.com"],
    "eventName": ["DeleteBucket", "PutBucketPolicy", "PutBucketAcl"],
    "errorCode": ["AccessDenied"]
  }
}
```

9. Click **Next**
10. For **Target**, select **Lambda function**
11. Choose your `S3SecurityResponder` function
12. Click **Next** then **Create rule**

### 4. Configure SNS Alerts
1. Go to **Amazon SNS** service
2. Click **Create topic**
3. Enter name: `S3SecurityAlerts`
4. Click **Create topic**
5. With the topic selected, click **Create subscription**
6. Choose protocol: **Email**
7. Enter your email address
8. Click **Create subscription**
9. Check your email and confirm the subscription

## Testing the System

1. **Trigger a test event**:
   - Navigate to **S3** service
   - Select a test bucket
   - Try to modify bucket ACLs (will be blocked if properly configured)

2. **Verify response**:
   - Check **Lambda** → **Monitor** → **Logs** for execution details
   - Search CloudTrail → Event history
   - Confirm you received an SNS alert email

## Security Best Practices

- Enable **S3 Block Public Access** at account level
- Use **bucket policies** instead of ACLs
- Enable **MFA Delete** for important buckets
- Regularly review **CloudTrail logs**

## Troubleshooting

| Issue | Solution |
|-------|----------|
| No events detected | Verify CloudTrail is logging S3 data events |
| Alerts not received | Check Lambda execution role has `sns:Publish` permission |
| Bucket not locked | Confirm Lambda has `s3:PutPublicAccessBlock` permission |

## License

Apache License 2.0
```
