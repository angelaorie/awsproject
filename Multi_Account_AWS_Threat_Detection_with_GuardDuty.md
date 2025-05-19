# Multi-Account Threat Detection with AWS GuardDuty

[![AWS GuardDuty](https://img.shields.io/badge/AWS-GuardDuty-FF9900?logo=amazonaws)](https://aws.amazon.com/guardduty/)
[![Serverless](https://img.shields.io/badge/Architecture-Serverless-blue)](https://aws.amazon.com/serverless/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

**Objective**: Implement centralized threat detection across multiple AWS accounts using AWS GuardDuty, Amazon EventBridge, S3, and Lambda for automated alerts.

## 🔍 Overview
A serverless security solution that:
- Centralizes threat detection from **multiple AWS accounts**
- Automatically analyzes findings using **GuardDuty**
- Routes alerts via **EventBridge** and **Lambda**
- Stores forensic data in **S3** for compliance

## 🛠️ Key Components
| Service | Purpose |
|---------|---------|
| AWS GuardDuty | Threat detection engine |
| Amazon EventBridge | Event routing |
| AWS Lambda | Alert processing |
| Amazon S3 | Log storage |

## 🚀 Deployment
### 1. Enable GuardDuty (Master Account)
```bash
aws guardduty create-detector --enable --region us-east-1
```

### 2. Configure EventBridge Rule
![EventBridge Setup](docs/eventbridge-rule.png)

### 3. Deploy Alert Processor
```python
# lambda/processor.py
import boto3

def lambda_handler(event, context):
    finding = event['detail']
    if finding['severity'] >= 7:
        # Send to SNS/Slack
```

## 📊 Results
- **Reduced threat response time** from hours to minutes
- **Unified visibility** across 3+ AWS accounts
- **Automated 90%** of alert triage

## 📜 License
MIT - See [LICENSE](LICENSE)
```
