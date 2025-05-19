# Multi-Account Threat Detection with AWS GuardDuty

[![AWS Console Guide](https://img.shields.io/badge/Setup-GUI_Instructions-FF9900?logo=amazonaws)](https://console.aws.amazon.com/)

## 🎯 Objective
Implement **centralized threat detection** across multiple AWS accounts using:
- **AWS GuardDuty** (Threat detection)
- **Amazon EventBridge** (Event routing)
- **AWS Lambda** (Alert processing)
- **Amazon S3** (Log storage)

---

## 🖥️ GUI Setup Guide

### 1. Enable GuardDuty (Master Account)
**Step-by-Step Console Actions:**
1. Login to [AWS Console](https://console.aws.amazon.com/)
2. Navigate to **GuardDuty** (Search or find under "Security, Identity, & Compliance")
3. Click **"Get Started"** → **"Enable GuardDuty"**
  
4. Under **Settings** → Enable:
   - ✔️ Malware Protection
   - ✔️ S3 Protection

---

### 2. Add Member Accounts (Optional)
1. In GuardDuty Console → **Accounts** → **Add Members**
2. Enter Account IDs to monitor:
  
3. Click **"Invite"** → Members must accept via **GuardDuty → Invitations**

---

### 3. Configure EventBridge Rule
**Console Path:**
`Services → EventBridge → Rules → Create rule`

| Field | Value |
|-------|-------|
| **Name** | `GuardDutyToS3` |
| **Event bus** | default |
| **Rule type** | Rule with event pattern |

**Event Pattern:**
```json
{
  "source": ["aws.guardduty"],
  "detail-type": ["GuardDuty Finding"]
}

```

**Add Target:**
1. Select **S3 bucket**
2. Choose existing bucket or create new (`guardduty-findings-<account-id>`)
3. Enable **SSE-S3 encryption**

---

### 4. Set Up Lambda Alert Processor
**Console Path:**
`Services → Lambda → Create function`

1. **Basic Information:**
   - Function name: `GuardDutyAlertProcessor`
   - Runtime: **Python 3.9**
   - Permissions: *Create new role with basic Lambda permissions*

2. **Paste Code:**
   ```python
   import boto3
   
   def lambda_handler(event, context):
       finding = event['detail']
       if finding['severity'] >= 7:  # Only high/critical
           sns = boto3.client('sns')
           sns.publish(
               TopicArn='YOUR_SNS_ARN',
               Message=f"🚨 GuardDuty Alert: {finding['type']}"
           )
   ```

3. **Connect to EventBridge:**
   - Go back to your EventBridge rule
   - Add **Lambda function** as additional target

---


## 🔍 Verification Steps
1. **Test with Sample Findings**  
   GuardDuty Console → **Settings** → **Generate sample findings**
2. **Check S3 Bucket**  
   Look for new files at:  
   `s3://guardduty-findings-<account-id>/AWSLogs/...`
3. **Monitor Alerts**  
   - Email/Slack notifications for high-severity findings
   - CloudWatch Logs for Lambda execution

---

## 📂 Repository Structure
```
docs/
├── enable-guardduty.png
├── event-pattern.png
└── lambda-editor.png
lambda/
└── guardduty_processor.py
README.md  # This file
```

---

## 💡 Pro Tips
- Use **AWS Free Tier** to minimize costs
- Set **CloudWatch Alarms** for Lambda errors
- Enable **S3 Versioning** for log integrity

[➡️ Full AWS Documentation](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html)
```
