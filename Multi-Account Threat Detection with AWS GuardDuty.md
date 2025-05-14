# Multi-Account AWS Threat Detection with GuardDuty

![AWS Security](https://img.shields.io/badge/AWS-Security-orange?logo=amazon-aws)
![GitHub GUI Guide](https://img.shields.io/badge/Guide-GUI_Steps-blue)

## Table of Contents
1. [Project Overview](#project-overview)
2. [GUI Setup Guide](#gui-setup-guide)
   - [Enable GuardDuty](#1-enable-guardduty)
   - [Configure EventBridge](#2-configure-eventbridge)
   - [Set Up Alerts](#3-set-up-alerts)
3. [Testing](#testing)
4. [Cost Optimization](#cost-optimization)
5. [Troubleshooting](#troubleshooting)

---

## Project Overview
Centralized security monitoring solution using:
- 🛡️ AWS GuardDuty (threat detection)
- 🔗 Amazon EventBridge (event routing)
- ⚡ AWS Lambda (alert processing)
- 📧 Amazon SNS (notifications)


---

## GUI Setup Guide

### 1. Enable GuardDuty
**Step-by-Step:**
1. Log in to [AWS Console](https://console.aws.amazon.com/)
2. Navigate to **GuardDuty** (under "Security, Identity, & Compliance")
3. Click **"Enable GuardDuty"**
4. Under **Settings**, enable "Malware Protection" (recommended)


---

### 2. Configure EventBridge
**Step-by-Step:**
1. Go to [EventBridge Console](https://console.aws.amazon.com/events/)
2. Click **"Create rule"**
   - **Name**: `GuardDutyToS3`
   - **Event bus**: default
3. Under **Event pattern**, paste:
   ```json
   {
     "source": ["aws.guardduty"],
     "detail-type": ["GuardDuty Finding"]
   }
 
   ```
4. Add target:
   - **Target type**: S3 bucket
   - **Bucket**: Create new `guardduty-findings-<account-id>`
   - Enable **encryption (SSE-S3)**

---

### 3. Set Up Alerts
#### Create SNS Topic
1. Go to [SNS Console](https://console.aws.amazon.com/sns/)
2. Click **"Create topic"**
   - **Type**: Standard
   - **Name**: `GuardDutyAlerts`
3. Add subscriptions:
   - **Protocol**: Email
   - **Endpoint**: Your email address
   - Confirm subscription via email

#### Deploy Lambda Function
1. Go to [Lambda Console](https://console.aws.amazon.com/lambda/)
2. Click **"Create function"**
   - **Name**: `GuardDutyAlertProcessor`
   - **Runtime**: Python 3.9
3. Paste the [provided Python code](#lambda-code)
4. Under **Configuration → Permissions**, attach `AmazonSNSFullAccess`


---

## Testing
Simulate threats via AWS CLI:
```bash
# SSH brute force (trigger UnauthorizedAccess finding)
for i in {1..5}; do
  ssh -o StrictHostKeyChecking=no -o ConnectTimeout=1 fakeuser@YOUR_EC2_IP
done
```

Verify in:
- GuardDuty **Findings** tab (within 15 mins)
- Your email inbox for alerts


---

## Troubleshooting
❌ **No findings appearing?**
1. Check CloudTrail is enabled:  
   ```bash
   aws cloudtrail describe-trails
   ```
2. Verify EventBridge rule shows **"Invocations" > 0**

❌ **Alerts not arriving?**
1. Confirm SNS subscription is **confirmed** (check email)
2. Check Lambda **CloudWatch logs** for errors

---

## License
MIT License - See [LICENSE](LICENSE) file

## Contributing
1. Fork this repository
2. Create a branch (`git checkout -b feature/improvement`)
3. Commit changes (`git commit -m 'Add new feature'`)
4. Push to branch (`git push origin feature/improvement`)
5. Open a Pull Request
```

