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




Deployment Guide
Prerequisites
AWS account with admin permissions

Domain name (optional for custom SSL)

Website files (HTML, CSS, images)
