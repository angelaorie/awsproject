markdown
# Multi-Account Threat Detection with AWS GuardDuty

![AWS Security](https://img.shields.io/badge/AWS-Security-orange?logo=amazon-aws) 
![Serverless](https://img.shields.io/badge/Architecture-Serverless-blue) 
![Free Tier](https://img.shields.io/badge/Cost-Free_Tier_Eligible-green)

A centralized security monitoring solution that uses AWS GuardDuty, EventBridge, Lambda, and SNS to detect and alert on threats across multiple AWS accounts.

## 📌 Features

- **Centralized threat detection** across multiple AWS accounts
- **Automated alerts** for high-severity findings via email/Slack
- **Long-term log storage** in S3 for compliance
- **Cost-optimized** using AWS Free Tier eligible services
- **Real-time response** to security incidents

## 🛠️ Architecture

```mermaid
graph TD
    A[GuardDuty] -->|Security Findings| B[EventBridge]
    B -->|All Findings| C[S3 Bucket]
    B -->|Critical Findings| D[Lambda]
    D --> E[SNS]
    E --> F[Email/Slack]
🔧 Prerequisites
AWS Account(s) with admin permissions

AWS CLI configured (for optional testing)

Email address or Slack webhook for alerts

🚀 Deployment Steps
1. Enable GuardDuty in Master Account
bash
# Enable GuardDuty via CLI (optional)
aws guardduty create-detector --enable
2. Configure EventBridge Rule
Navigate to Amazon EventBridge

Create rule with pattern:

json
{
  "source": ["aws.guardduty"],
  "detail-type": ["GuardDuty Finding"]
}
Set S3 bucket as target

3. Set Up Alerts
Create SNS topic

Deploy Lambda function with provided Python code

Subscribe your email/Slack to SNS

💰 Cost Optimization
Use S3 Lifecycle Policies to move old logs to Glacier

Filter only high-severity events (severity ≥ 7)

Disable GuardDuty after testing if not needed

🧪 Testing
Simulate threats to verify detection:

bash
# SSH brute force simulation
for i in {1..10}; do 
  ssh -o StrictHostKeyChecking=no -o ConnectTimeout=1 fakeuser@YOUR_EC2_IP
done

# Suspicious API calls
aws iam list-users --region us-east-1
aws s3api list-buckets --region eu-west-1
📄 License
This project is licensed under the MIT License - see the LICENSE file for details.

🤝 Contributing
Pull requests are welcome! For major changes, please open an issue first to discuss what you would like to change.
