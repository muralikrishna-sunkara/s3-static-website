# S3 Static Website with CloudFront CDN

A Terraform project that deploys a static website to AWS S3 with CloudFront CDN distribution for global content delivery.

## Overview

This project creates a secure, scalable static website infrastructure on AWS with the following components:

- **S3 Bucket**: Private bucket for hosting static content with versioning enabled
- **CloudFront CDN**: Global content delivery network for low-latency access
- **Origin Access Identity (OAI)**: Secure access between CloudFront and S3
- **HTTPS**: Automatic redirect to HTTPS for secure connections

## Architecture

The infrastructure follows AWS best practices:

1. S3 bucket blocks all public access for security
2. CloudFront Origin Access Identity provides secure access to S3
3. CloudFront serves content over HTTPS with caching enabled
4. S3 versioning enabled for content history and rollback capability

## Prerequisites

- [Terraform](https://www.terraform.io/downloads) >= 1.0
- AWS CLI configured with appropriate credentials
- AWS account with permissions to create S3, CloudFront, and IAM resources

## Project Structure

```
.
├── main.tf           # Main Terraform configuration
├── index.html        # Static website content
├── .gitignore        # Git ignore rules for Terraform files
└── README.md         # This file
```

## Configuration Details

### AWS Resources Created

- **S3 Bucket**: `devops-links-website-{account-id}`
- **CloudFront Distribution**: With default SSL certificate
- **S3 Bucket Policy**: Allows CloudFront OAI to read objects
- **Public Access Block**: Prevents accidental public exposure

### Terraform Resources

| Resource | Purpose |
|----------|---------|
| `aws_s3_bucket.website` | S3 bucket for hosting content |
| `aws_s3_bucket_public_access_block.website` | Blocks all public access |
| `aws_s3_bucket_versioning.website` | Enables object versioning |
| `aws_cloudfront_origin_access_identity.website` | OAI for secure S3 access |
| `aws_s3_bucket_policy.website` | Bucket policy for CloudFront access |
| `aws_s3_bucket_website_configuration.website` | Static website configuration |
| `aws_s3_object.index` | Uploads index.html to S3 |
| `aws_cloudfront_distribution.website` | CDN distribution |

## Usage

### 1. Initialize Terraform

```bash
terraform init
```

### 2. Review the Plan

```bash
terraform plan
```

### 3. Deploy Infrastructure

```bash
terraform apply
```

Type `yes` when prompted to confirm the deployment.

### 4. Access Your Website

After successful deployment, Terraform will output the website URL:

```bash
terraform output website_url
```

Visit the CloudFront URL to access your static website.

### 5. Update Website Content

1. Modify [index.html](index.html)
2. Run `terraform apply` to upload the updated content
3. CloudFront cache will serve the new content (may take a few minutes due to caching)

### 6. Destroy Infrastructure

When you no longer need the infrastructure:

```bash
terraform destroy
```

Type `yes` when prompted to confirm deletion.

## Outputs

| Output | Description |
|--------|-------------|
| `s3_bucket_name` | Name of the S3 bucket |
| `cloudfront_domain_name` | CloudFront distribution domain |
| `website_url` | Full HTTPS URL to access the website |
| `oai_id` | CloudFront Origin Access Identity ID |

## Configuration

### Region

The default region is set to `eu-central-1`. To change it, modify the provider configuration in [main.tf:12](main.tf#L12):

```hcl
provider "aws" {
  region = "eu-central-1"  # Change to your preferred region
}
```

### Bucket Name

The S3 bucket name includes your AWS account ID for uniqueness: `devops-links-website-{account-id}`

### Cache Settings

CloudFront cache settings in [main.tf:131-134](main.tf#L131-L134):

- **Min TTL**: 0 seconds
- **Default TTL**: 3600 seconds (1 hour)
- **Max TTL**: 86400 seconds (24 hours)

## Security Features

- S3 bucket has all public access blocked
- CloudFront uses Origin Access Identity for secure S3 access
- HTTPS enforced with automatic HTTP to HTTPS redirect
- S3 versioning enabled for content recovery
- Compression enabled for faster delivery

## Cost Considerations

- **S3**: Pay for storage and requests
- **CloudFront**: Free tier includes 1TB data transfer out and 10M HTTPS requests per month
- **Data Transfer**: Charges apply for data transfer beyond free tier

Always monitor your AWS billing dashboard.

## Troubleshooting

### Website not accessible

- Wait 5-10 minutes after deployment for CloudFront distribution to propagate globally
- Check CloudFront distribution status in AWS Console (should be "Deployed")

### Content not updating

- CloudFront caching may serve old content
- Wait for cache TTL to expire or invalidate CloudFront cache manually
- Use AWS CLI to create invalidation: `aws cloudfront create-invalidation --distribution-id <ID> --paths "/*"`

### Permission errors

- Ensure AWS credentials have necessary permissions
- Check IAM policies allow S3, CloudFront, and IAM operations

## License

This is a DevOps learning project.

## Author

Created as part of DevOps labs for learning AWS infrastructure as code with Terraform.
