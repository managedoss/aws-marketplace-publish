# Publish Docker images to AWS Marketplace


This is a simple GitHub Action to publish new versions of Docker images to AWS Marketplace.
At the moment, it only supports adding a new version of an existing product. Contributions are welcome!

## Usage

```yaml
name: Publish to AWS Marketplace

on:
  push:
    tags:
      - 'v[0-9]+.[0-9]+.[0-9]+'

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1

      - name: Publish to AWS Marketplace
        uses: managedoss/aws-marketplace-publish@v1.0.3
        with:
          product-id: ${{ env.AWS_PRODUCT_ID }}
          version: ${{ env.version }}
          registry: 709825985650.dkr.ecr.us-east-1.amazonaws.com/organization/repo:${{ env.version }}
          release-notes: ${{ github.event.release.body }}
          resources: |-
            [
              {
                "Name": "Terraform Module",
                "Url": "https://github.com/managedoss/terraform-aws-superset"
              },
              {
                "Name": "Cloudformation Template",
                "Url": "https://us-east-1.console.aws.amazon.com/cloudformation/home#/stacks/quickcreate?templateUrl=https://managedoss-shared-web-assets.s3.amazonaws.com/cloudformation.yml"
              }
            ]
          description: This is description of your product.
          usage-instructions: This is how you use your product.
```

## Inputs

| Name | Description | Required |
| --- | --- | --- |
| `product-id` | The ID of the product to publish. | Yes |
| `version` | The version of the product to publish. | Yes |
| `registry` | The Docker image to publish. | Yes |
| `release-notes` | Release notes for the new version. Must not be an empty. | Yes |
| `description` | Description of the product. | Yes |
| `resources` | A stringified JSON object with deployment resources for your product. | No |
| `usage-instructions` | Usage instructions for the product. | Yes |
