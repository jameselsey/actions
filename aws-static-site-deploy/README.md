# aws-static-site-deploy

Sync a pre-built static site to an S3 bucket and invalidate the CloudFront distribution cache. HTML files and static assets are synced with separate cache-control headers. Waits for the CloudFront invalidation to complete before finishing.

## Usage

```yaml
- name: Configure AWS credentials
  uses: aws-actions/configure-aws-credentials@v4
  with:
    role-to-assume: arn:aws:iam::${{ secrets.AWS_ACCOUNT_ID }}:role/github-deploy-role
    aws-region: us-east-1

- name: Deploy to S3 and invalidate CloudFront
  uses: jameselsey/actions/aws-static-site-deploy@main
  with:
    build-path: './dist'
    s3-bucket: ${{ secrets.S3_BUCKET_NAME }}
    cloudfront-distribution-id: ${{ secrets.CLOUDFRONT_DISTRIBUTION_ID }}
```

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `build-path` | Yes | — | Absolute or relative path to the directory containing the built static files |
| `s3-bucket` | Yes | — | Name of the S3 bucket to sync files into |
| `cloudfront-distribution-id` | Yes | — | CloudFront distribution ID to invalidate after upload |
| `s3-prefix` | No | `` | Optional key prefix (folder) within the S3 bucket |
| `cache-control-html` | No | `max-age=300, must-revalidate` | Cache-Control header applied to `.html` files |
| `cache-control-assets` | No | `max-age=86400, immutable` | Cache-Control header applied to all other files (JS, CSS, images, fonts, etc.) |

## Prerequisites

- AWS credentials must be configured before calling this action (e.g. using `aws-actions/configure-aws-credentials`).
- The IAM role or user must have `s3:PutObject`, `s3:DeleteObject`, `s3:ListBucket`, and `cloudfront:CreateInvalidation` + `cloudfront:GetInvalidation` permissions.
- The S3 bucket and CloudFront distribution must already exist (created separately, e.g. via CloudFormation).
- The AWS CLI must be available on the runner (it is pre-installed on all GitHub-hosted runners).
