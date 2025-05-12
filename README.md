# 🛡️ Secure Static Website Hosting on AWS

[![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com)
[![Terraform](https://img.shields.io/badge/terraform-%235835CC.svg?style=for-the-badge&logo=terraform&logoColor=white)](https://www.terraform.io)

This project securely hosts a static website on AWS using:
- **Amazon S3** for static site hosting
- **CloudFront** for global content delivery
- **AWS Certificate Manager (ACM)** for HTTPS
- **AWS WAF** for protection against common web exploits

## 🔐 Security Best Practices

| Feature | Implementation |
|---------|---------------|
| Authentication | IAM users with MFA enabled |
| Data Protection | S3 access logging enabled |
| Encryption | CloudFront enforces HTTPS |
| Web Protection | WAF blocks XSS, SQLi, and bad bots |
| Access Control | Least privilege IAM policies |

## 🖼️ Architecture

```mermaid
graph TD
    A[User] --> B[CloudFront]
    B --> C[S3 Bucket]
    B --> D[AWS WAF]
    C --> E[Origin Access Control]
    D --> F[Malicious Traffic Filter]

## 🚀 Deployment Guide

### Prerequisites
- AWS account with admin permissions
- Domain name (optional for custom SSL)
- Website files (HTML, CSS, images)

### 1. S3 Bucket Setup
```bash
aws s3 mb s3://mywebsite-secure
aws s3 website s3://mywebsite-secure --index-document index.html
```

### 2. Upload Website Content
```bash
aws s3 sync ./website-files s3://mywebsite-secure
```

### 3. CloudFront Configuration
1. Create OAC (Origin Access Control)
2. Create distribution with:
   - S3 website endpoint as origin
   - OAC for secure access
   - HTTPS enforcement

### 4. Security Setup
```bash
# Attach WAF Web ACL
aws cloudfront update-distribution \
    --id E3HHR6JYLCW3MU \
    --web-acl-id your-web-acl-id
```

## 🛡️ Security Configuration

### S3 Bucket Policy
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::mywebsite-secure/*",
      "Condition": {
        "StringEquals": {
          "AWS:SourceArn": "arn:aws:cloudfront::123456789012:distribution/E3HHR6JYLCW3MU"
        }
      }
    }
  ]
}
```

### WAF Rules
- AWSManagedRulesCommonRuleSet
- Rate limiting rules
- Geo-restriction (optional)

## 📂 Project Structure

```
secure-website/
├── infra/
│   ├── main.tf            # Terraform configuration
│   └── variables.tf       # Environment variables
├── website/
│   ├── index.html         # Main page
│   ├── styles.css         # Stylesheet
│   └── images/            # Asset directory
└── README.md              # This documentation
```

## 🧹 Cleanup

To remove all resources:
```bash
terraform destroy
```

## 📚 Resources
- [AWS S3 Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)
- [CloudFront Best Practices](https://aws.amazon.com/cloudfront/best-practices/)
- [WAF Rule Groups](https://docs.aws.amazon.com/waf/latest/developerguide/aws-managed-rule-groups-list.html)

---

💡 **Tip:** For production environments, consider adding:
- CI/CD pipeline for automated deployments
- Monitoring with CloudWatch
- DDoS protection with AWS Shield
