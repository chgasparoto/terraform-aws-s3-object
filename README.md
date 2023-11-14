# Terraform AWS S3 Object module

Terraform module to handle S3 buckets and bucket objects and resources on AWS.

These types of resources are supported:

- [S3 Bucket](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket)
- [S3 Bucket Ownership Controls](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_ownership_controls)
- [S3 Bucket-level Public Access Block](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_public_access_block)
- [S3 Bucket ACL](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_acl)
- [S3 Bucket Policy](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_policy)
- [S3 Bucket Versioning](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_versioning)
- [S3 Bucket Website Configuration](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_website_configuration)
- [S3 Bucket Logging](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_logging)
- [S3 Object](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_object)

## Usage

```hcl
module "bucket" {
  source = "github.com/chgasparoto/terraform-aws-s3-object"

  name   = "my-super-unique-bucket-name"
  acl    = "public-read"
  policy = {
    json = jsonencode({
      Version = "2012-10-17"
      Statement = [
        {
          Sid       = "PublicReadGetObject"
          Effect    = "Allow"
          Principal = "*"
          Action    = "s3:GetObject"
          Resource = [
            "arn:aws:s3:::my-super-unique-bucket-name/*",
          ]
        },
      ]
    })
  }

  versioning = {
    status = "Enabled"
  }

  # This property activates the module to upload the files to the bucket.
  filepath = "path/to/my/website/files"
  website = {
    index_document = "index.html"
    error_document = "error.html"
  }

  logging = {
    target_bucket = module.logs.name
    target_prefix = "access/"
  }
}
```

## Examples

- [Simple Bucket](examples/simple-bucket)
- [Static Website](examples/static-website)

## Requirements

| Name      | Version  |
| --------- | -------- |
| terraform | >= 1.0.0 |
| aws       | >= 4.0.0 |

## Inputs

| Name                    | Description                                                                                       |                                                       Type                                                        |        Default         | Required |
| ----------------------- | ------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------: | :--------------------: | :------: |
| name                    | Bucket unique name                                                                                |                                                     `string`                                                      |         `null`         |    ✅    |
| ownership               | Object ownership                                                                                  |                                                     `string`                                                      | `BucketOwnerPreferred` |          |
| acl                     | Bucket ACL                                                                                        |                                                     `string`                                                      |       `private`        |          |
| policy                  | Bucket Policy                                                                                     |                                            `object({ json = string })`                                            |                        |  `null`  |
| block_public_acls       | Whether to block public ACLs                                                                      |                                                      `bool`                                                       |         `true`         |          |
| block_public_policy     | Whether to block public policiy                                                                   |                                                      `bool`                                                       |         `true`         |          |
| ignore_public_acls      | Whether to ignore public ACLs                                                                     |                                                      `bool`                                                       |         `true`         |          |
| restrict_public_buckets | Whether to restrict public buckets                                                                |                                                      `bool`                                                       |         `true`         |          |
| force_destroy           | Whether or not to force destroy the bucket                                                        |                                                      `bool`                                                       |        `false`         |          |
| tags                    | Bucket Tags                                                                                       |                                                   `map(string)`                                                   |          `{}`          |          |
| key_prefix              | Prefix to put your key(s) inside the bucket. E.g.: logs -> all files will be uploaded under logs/ |                                                     `string`                                                      |                        |          |
| filepath                | The local path where the desired files will be uploaded to the bucket                             |                                                     `string`                                                      |                        |          |
| versioning              | Object containing versioning configuration                                                        | <pre>object({<br>expected_bucket_owner: string<br>status: string<br>mfa: string<br>mfa_delete: string<br>})</pre> |          `{}`          |          |
| website                 | Map containing website configuration                                                              |                                                   `map(string)`                                                   |          `{}`          |          |
| logging                 | Map containing logging configuration                                                              |                                                   `map(string)`                                                   |          `{}`          |          |

## Outputs

| Name                 | Description                                                                                                                                                          |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| arn                  | The ARN of the bucket. Will be of format `arn:aws:s3:::bucketname`                                                                                                   |
| name                 | Bucket name                                                                                                                                                          |
| website              | The website endpoint, if the bucket is configured with a website. If not, this will be an empty string                                                               |
| regional_domain_name | The bucket region-specific domain name. E.g.: `bucketname.s3.eu-central-1.amazonaws.com`                                                                             |
| domain_name          | The bucket domain name. Will be of format `bucketname.s3.amazonaws.com`                                                                                              |
| website_domain       | The domain of the website endpoint, if the bucket is configured with a website. If not, this will be an empty string. This is used to create Route 53 alias records. |
| hosted_zone_id       | The Route 53 Hosted Zone ID for this bucket's region                                                                                                                 |
| objects              | List of objects uploaded to the bucket                                                                                                                               |

## Authors

Module managed by [Cleber Gasparoto](https://github.com/chgasparoto)

## License

[MIT](LICENSE)
