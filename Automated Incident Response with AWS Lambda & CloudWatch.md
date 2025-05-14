



Prerequisites
AWS account with admin permissions

AWS CLI configured (aws configure)

Python 3.9+ (for Lambda development)

Setup
1. Create S3 Test Bucket
bash
aws s3api create-bucket --bucket test-security-bucket-$(aws sts get-caller-identity --query Account --output text)
2. Deploy Lambda Function
Create new Lambda (Python 3.9) named S3SecurityResponder

Paste this code:

python
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
3. Configure EventBridge Rule
json
{
  "source": ["aws.s3"],
  "detail-type": ["AWS API Call via CloudTrail"],
  "detail": {
    "eventSource": ["s3.amazonaws.com"],
    "eventName": ["DeleteBucket", "PutBucketPolicy"],
    "errorCode": ["AccessDenied"]
  }
}
Target: Your Lambda function

4. Set Up SNS Alerts
Create SNS topic SecurityAlerts

Subscribe your email/SMS

Testing
bash
# Simulate unauthorized action (will fail)
aws s3api delete-bucket --bucket test-security-bucket-YOUR_ACCOUNT_ID
Verify:

Lambda execution in CloudWatch Logs

SNS alert received

Bucket public access blocked (check S3 console)

Permissions
Ensure Lambda execution role has:

AmazonS3FullAccess

AmazonSNSFullAccess

Troubleshooting
Issue	Solution
No Lambda trigger	Check EventBridge rule pattern matches CloudTrail events
SNS alerts missing	Verify Lambda has sns:Publish permissions
Bucket not quarantined	Check for s3:PutPublicAccessBlock permissions
License
MIT

