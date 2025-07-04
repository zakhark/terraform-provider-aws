---
subcategory: "CloudWatch Logs"
layout: "aws"
page_title: "AWS: aws_cloudwatch_log_account_policy"
description: |-
  Provides a CloudWatch Log Account Policy resource.
---

# Resource: aws_cloudwatch_log_account_policy

Provides a CloudWatch Log Account Policy resource.

## Example Usage

### Account Data Protection Policy

```terraform
resource "aws_cloudwatch_log_account_policy" "data_protection" {
  policy_name = "data-protection"
  policy_type = "DATA_PROTECTION_POLICY"
  policy_document = jsonencode({
    Name    = "DataProtection"
    Version = "2021-06-01"

    Statement = [
      {
        Sid            = "Audit"
        DataIdentifier = ["arn:aws:dataprotection::aws:data-identifier/EmailAddress"]
        Operation = {
          Audit = {
            FindingsDestination = {}
          }
        }
      },
      {
        Sid            = "Redact"
        DataIdentifier = ["arn:aws:dataprotection::aws:data-identifier/EmailAddress"]
        Operation = {
          Deidentify = {
            MaskConfig = {}
          }
        }
      }
    ]
  })
}
```

### Subscription Filter Policy

```terraform
resource "aws_cloudwatch_log_account_policy" "subscription_filter" {
  policy_name = "subscription-filter"
  policy_type = "SUBSCRIPTION_FILTER_POLICY"
  policy_document = jsonencode(
    {
      DestinationArn = aws_lambda_function.test.arn
      FilterPattern  = "test"
    }
  )
  selection_criteria = "LogGroupName NOT IN [\"excluded_log_group_name\"]"
}
```

### Field Index Policy

```terraform
resource "aws_cloudwatch_log_account_policy" "field_index" {
  policy_name = "field-index"
  policy_type = "FIELD_INDEX_POLICY"
  policy_document = jsonencode(
    {
      Fields = [
        "field1",
        "field2"
      ]
    }
  )
}
```

## Argument Reference

This resource supports the following arguments:

* `region` - (Optional) Region where this resource will be [managed](https://docs.aws.amazon.com/general/latest/gr/rande.html#regional-endpoints). Defaults to the Region set in the [provider configuration](https://registry.terraform.io/providers/hashicorp/aws/latest/docs#aws-configuration-reference).
* `policy_document` - (Required) Text of the account policy. Refer to the [AWS docs](https://docs.aws.amazon.com/cli/latest/reference/logs/put-account-policy.html) for more information.
* `policy_type` - (Required) Type of account policy. One of `DATA_PROTECTION_POLICY`, `SUBSCRIPTION_FILTER_POLICY`, `FIELD_INDEX_POLICY` or `TRANSFORMER_POLICY`. You can have one account policy per type in an account.
* `policy_name` - (Required) Name of the account policy.
* `scope` - (Optional) Currently defaults to and only accepts the value: `ALL`.
* `selection_criteria` - (Optional) - Criteria for applying a subscription filter policy to a selection of log groups. The only allowable criteria selector is `LogGroupName NOT IN []`.

## Attribute Reference

This resource exports no additional attributes.

## Import

In Terraform v1.5.0 and later, use an [`import` block](https://developer.hashicorp.com/terraform/language/import) to import this resource using the `policy_name` and `policy_type` fields separated by `:`. For example:

```terraform
import {
  to = aws_cloudwatch_log_account_policy.example
  id = "my-account-policy:SUBSCRIPTION_FILTER_POLICY"
}
```

Using `terraform import`, import this resource using the `policy_name` and `policy_type` separated by `:`. For example:

```console
% terraform import aws_cloudwatch_log_account_policy.example "my-account-policy:SUBSCRIPTION_FILTER_POLICY"
```
