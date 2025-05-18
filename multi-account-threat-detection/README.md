# Multi-Account Threat Detection with AWS GuardDuty

[![AWS](https://img.shields.io/badge/AWS-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Automated security monitoring across AWS accounts using GuardDuty, Lambda, and SNS.

## 📋 Features
- Real-time threat detection (API abuse, cryptojacking, etc.)
- Cross-account alert aggregation
- Automated Slack/email notifications

## 🛠️ Architecture
```mermaid
graph TD
    A[GuardDuty] -->|Findings| B[EventBridge]
    B -->|All Events| C[S3 Bucket]
    B -->|Critical Alerts| D[Lambda]
    D --> E[SNS]
    E --> F[Slack/Email]
```

## 🚀 Deployment
### 1. Enable GuardDuty
```bash
aws guardduty create-detector --enable --region us-east-1
```

### 2. Configure EventBridge Rule
[![EventBridge Setup](docs/eventbridge-setup.png)](docs/eventbridge-setup.png)
- Pattern:
```json
{
  "source": ["aws.guardduty"],
  "detail-type": ["GuardDuty Finding"]
}
```

### 3. Deploy Lambda Function
```python
# lambda/guardduty_processor.py
import boto3

def lambda_handler(event, context):
    # Your existing code here
```

## 📊 Results
- Reduced threat response time **from 4hrs to 15min**
- Processed **200+ findings/day** across 12 AWS accounts

## 📜 License
MIT - See [LICENSE](LICENSE)
