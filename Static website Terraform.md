🌐 Terraform AWS Static Website with HTTPS (S3 + CloudFront)
Terraform Version
AWS Provider

This Terraform project deploys a secure static website to AWS using:

S3 for storage

CloudFront for global CDN delivery

HTTPS encryption (via CloudFront's free SSL)

Architecture Diagram

🚀 Features
✅ Automated deployment with Terraform

✅ Secure HTTPS delivery

✅ Global CDN caching

✅ Private S3 bucket (no public access)

✅ Automatic index document handling

📋 Prerequisites
Before you begin:

Terraform installed (v1.0+)

AWS account with permissions

AWS credentials configured via:

bash
export AWS_ACCESS_KEY_ID="your-access-key"
export AWS_SECRET_ACCESS_KEY="your-secret-key"
🛠️ Deployment Steps
1. Clone the Repository
bash
git clone https://github.com/yourusername/terraform-s3-website.git
cd terraform-s3-website
2. Initialize Terraform
bash
terraform init
3. Review and Deploy
bash
terraform plan  # Preview changes
terraform apply # Deploy infrastructure
Type yes when prompted to confirm.

4. Access Your Website
After deployment completes (5-10 minutes), Terraform will output your CloudFront URL:

cloudfront_url = "https://d123abc456def.cloudfront.net"
📂 Project Structure
terraform-s3-website/
├── main.tf          # Main infrastructure configuration
├── index.html       # Sample website file (customize this)
├── README.md        # This documentation
└── .gitignore       # Excludes Terraform state files
🧹 Clean Up
To remove all resources:

bash
terraform destroy
🚨 Troubleshooting
Issue	Solution
Access BentSet error	Verify S3 bucket policy allows CloudFront OAI
CloudFront returns 403	Ensure index.html exists in S3 bucket
Changes not updating?	Create CloudFront invalidation: terraform apply -replace="aws_cloudfront_distribution.website"
SSL certificate issues	Wait 30-60 minutes for DNS propagation
📚 Resources
Terraform AWS Provider Docs

CloudFront Developer Guide

📜 License
MIT License - See LICENSE for details.

