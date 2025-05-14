# 🛡️ Secure Static Website Hosting on AWS (GUI Version)

[![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com)

## 🖥️ Complete AWS Console Walkthrough

### 1. Create S3 Bucket
1. **Navigate to**: [S3 Console](https://s3.console.aws.amazon.com/)
2. Click **Create bucket**
3. Enter:
   - **Bucket name**: `mywebsite-secure` (must be globally unique)
   - **Region**: Select your preferred region (e.g., us-east-1)
4. Under **Block Public Access**, UNCHECK "Block all public access"
   - *Note: We'll secure access via OAC later*
5. Click **Create bucket**

### 2. Enable Static Website Hosting
1. Select your bucket → **Properties** tab
2. Scroll to **Static website hosting**
3. Click **Edit** → Select **Enable**
4. Configure:
   - **Hosting type**: Host a static website
   - **Index document**: index.html
   - **Error document**: (optional) error.html
5. Click **Save changes**

### 3. Upload Website Files
1. Go to **Objects** tab
2. Click **Upload** → **Add files**
3. Select all your website files (index.html, CSS, images)
4. Click **Upload**

### 4. Create CloudFront Distribution
1. **Navigate to**: [CloudFront Console](https://console.aws.amazon.com/cloudfront/)
2. Click **Create distribution**
3. **Origin settings**:
   - **Origin domain**: Select your S3 bucket website endpoint (e.g., `mywebsite-secure.s3-website-us-east-1.amazonaws.com`)
   - **Name**: Auto-populated
   - **Origin access**: Select **Origin Access Control (OAC)**
   - Click **Create control setting**
     - Name: `website-oac`
     - Signing behavior: **Sign requests**
     - Click **Create**

4. **Default cache behavior**:
   - **Viewer protocol policy**: Redirect HTTP to HTTPS
   - **Cache policy**: CachingOptimized

5. **Settings**:
   - **Default root object**: index.html
   - **Alternate domain name**: (Optional) Enter your custom domain
   - **Custom SSL certificate**: (If using custom domain) Select from ACM

6. Click **Create distribution** (Deployment takes ~15 minutes)


### 5. Configure S3 Bucket Policy
1. Go back to your S3 bucket → **Permissions** tab
2. Under **Bucket policy**, click **Edit**
3. Paste this policy (replace distribution ARN):
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
          "AWS:SourceArn": "arn:aws:cloudfront::YOUR_ACCOUNT_ID:distribution/YOUR_DISTRIBUTION_ID"
        }
      }
    }
  ]
}

```
4. Click **Save changes**

### 6. Set Up AWS WAF
1. **Navigate to**: [WAF Console](https://console.aws.amazon.com/wafv2/)
2. Click **Create web ACL**
3. Configure:
   - **Name**: website-protection
   - **Resource type**: CloudFront distributions
   - **Add AWS Managed Rules**:
     - AWSManagedRulesCommonRuleSet (blocks common exploits)
     - AWSManagedRulesKnownBadInputsRuleSet (blocks SQLi/XSS)
4. Click **Create**
5. Attach to CloudFront:
   - Go back to your CloudFront distribution
   - **Security** tab → Edit WAF association
   - Select your new Web ACL
   - Click **Save changes**

## 🎉 Final Verification
1. Wait ~15 minutes for CloudFront deployment
2. Access your distribution URL (found in CloudFront console)
3. Test:
   - HTTPS works (`https://d123...cloudfront.net`)
   - WAF blocks malicious requests (try `example.com/<script>`)
   - S3 direct access is blocked (try S3 website URL)

## 📝 Troubleshooting

| Issue | Solution |
|-------|----------|
| 403 Forbidden | Verify S3 bucket policy matches CloudFront ARN |
| Mixed Content | Ensure all resources use HTTPS |
| WAF not blocking | Check rule action is set to "Block" |
| SSL not working | Verify ACM certificate is in us-east-1 |

```
