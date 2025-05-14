# Automated Incident Response with AWS Lambda & S3

Automatically detect and respond to unauthorized S3 actions using AWS Lambda, CloudTrail, and EventBridge.

## Features
- 🚨 Real-time detection of unauthorized S3 API calls (`DeleteBucket`, `PutBucketPolicy`, etc.)
- ⚡ Automatic remediation (quarantine buckets, revoke IAM keys)
- 📧 Alerting via Amazon SNS (email/SMS)
- 🔍 Full CloudTrail integration for audit trails

## Architecture
```mermaid
graph TD
    A[CloudTrail] -->|Logs S3 API Calls| B(EventBridge)
    B -->|Triggers on AccessDenied| C[Lambda]
    C -->|Isolate Bucket| D[S3]
    C -->|Send Alerts| E[SNS]
    C -->|Revoke Keys| F[IAM]
