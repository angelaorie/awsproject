# Multi-Account Threat Detection with AWS GuardDuty

[![AWS](https://img.shields.io/badge/AWS-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com)

Automated security monitoring across AWS accounts using GuardDuty, Lambda, and SNS.

## 📋 Features
- Real-time threat detection (API abuse, cryptojacking, etc.)
- Cross-account alert aggregation
- Automated **Slack**/email notifications *(fixed "Stack" typo)*

## 🛠️ Architecture
![Architecture Diagram](docs/architecture.png)  
*(You'll upload this image in Step 3)*

## 🚀 Quick Start
```bash
# Enable GuardDuty
aws guardduty create-detector --enable
