
**References:** https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html

#### MFA

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowAllUsersToListAccounts",
            "Effect": "Allow",
            "Action": [
                "iam:ListAccountAliases",
                "iam:ListUsers"
            ],
            "Resource": [
                "arn:aws:iam::857857857857:user/*"
            ]
        },
        {
            "Sid": "AllowIndividualUserToSeeTheirAccountInformation",
            "Effect": "Allow",
            "Action": [
                "iam:GetAccountPasswordPolicy",
                "iam:GetAccountSummary",
                "iam:GetLoginProfile"
            ],
            "Resource": [
                "arn:aws:iam::857857857857:user/${aws:username}"
            ]
        },
        {
            "Sid": "AllowIndividualUserToListTheirMFA",
            "Effect": "Allow",
            "Action": [
                "iam:ListVirtualMFADevices",
                "iam:ListMFADevices"
            ],
            "Resource": [
                "arn:aws:iam::857857857857:mfa/*",
                "arn:aws:iam::857857857857:user/${aws:username}"
            ]
        },
        {
            "Sid": "AllowIndividualUserToManageThierMFA",
            "Effect": "Allow",
            "Action": [
                "iam:CreateVirtualMFADevice",
                "iam:DeactivateMFADevice",
                "iam:DeleteVirtualMFADevice",
                "iam:EnableMFADevice",
                "iam:ResyncMFADevice"
            ],
            "Resource": [
                "arn:aws:iam::857857857857:mfa/${aws:username}",
                "arn:aws:iam::857857857857:user/${aws:username}"
            ]
        },
        {
            "Sid": "DoNotAllowAnythingOtherThanAboveUnlessMFAd",
            "Effect": "Deny",
            "NotAction": "iam:*",
            "Resource": "*",
            "Condition": {
                "Bool": {
                    "aws:MultiFactorAuthPresent": "false",
                    "aws:ViaAWSService": "false"
                },
                "StringNotEqualsIfExists": {
                    "aws:SourceVpc": [
                        "vpc-857abc857abc875aa",
                        "vpc-857cba857cba875bb"
                    ]
                },
                "StringNotLikeIfExists": {
                    "aws:SourceVpce": "vpce-*"
                }
            }
        }
    ]
}
```

#### Source IP / VPC

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "SourceIpVpcWhitelist",
            "Effect": "Deny",
            "Action": "*",
            "Resource": "*",
            "Condition": {
                "Bool": {
                    "aws:ViaAWSService": "false"
                },
                "NotIpAddressIfExists": {
                    "aws:SourceIp": [
                        "8.5.7.11/32",
                        "8.5.7.22/32",
                        "8.5.7.33/32"
                    ]
                },
                "StringNotEqualsIfExists": {
                    "aws:SourceVpc": [
                        "vpc-857abc857abc875aa",
                        "vpc-857cba857cba875bb"
                    ]
                },
                "StringNotLikeIfExists": {
                    "aws:SourceVpce": "vpce-*"
                }
            }
        }
    ]
}
```

#### S3 Bucket Level Policy

```json
{
    "Version": "2012-10-17",
    "Id": "VPCs and RoleArns and SourceIPs",
    "Statement": [
        {
            "Sid": "VPCs and SourceIPs",
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::BUCKET_NAME",
                "arn:aws:s3:::BUCKET_NAME/*"
            ],
            "Condition": {
                "ForAllValues:StringNotEquals": {
                    "aws:SourceVpc": [
                        "vpc-857abc857abc875aa",
                        "vpc-857cba857cba875bb"
                    ]
                },
                "ForAllValues:ArnNotLike": {
                    "aws:PrincipalArn": [
                        "arn:aws:iam::857857857857:role/YourRoleName",
                        "arn:aws:iam::361361361361:role/YourRoleName",
                        "arn:aws:iam::857857857857:role/*Role*",
                        "arn:aws:iam::361361361361:role/*Role*"
                    ]
                },
                "ForAllValues:NotIpAddress": {
                    "aws:SourceIp": [
                        "8.5.7.11/32",
                        "8.5.7.22/32",
                        "8.5.7.33/32"
                    ]
                }
            }
        }
    ]
}
```

#### Service Policy Example: KMS + S3 + Lambda

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "kms:Decrypt",
                "kms:Encrypt",
                "kms:DescribeKey",
                "kms:ReEncrypt*",
                "kms:GenerateDataKey*",
                "s3:ListBucket",
                "s3:ListMultipartUploadParts",
                "s3:ListBucketMultipartUploads",
                "s3:AbortMultipartUpload",
                "s3:GetObject",
                "s3:GetObjectTagging",
                "s3:PutObject",
                "s3:DeleteObject",
                "lambda:GetFunction",
                "lambda:GetAlias",
                "lambda:InvokeFunction",
                "lambda:InvokeAsync"
            ],
            "Resource": [
                "arn:aws:kms:us-east-1:857857857857:key/*",
                "arn:aws:s3:::bucket-svc-prefix-*",
                "arn:aws:lambda:us-east-1:857857857857:function:*"
            ],
            "Condition": {
                "ForAllValues:StringEquals": {
                    "aws:SourceVpc": [
                        "vpc-857abc857abc875aa"
                    ]
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListAllMyBuckets"
            ],
            "Resource": [
                "arn:aws:s3:::*"
            ],
            "Condition": {
                "ForAllValues:StringEquals": {
                    "aws:SourceVpc": [
                        "vpc-857abc857abc875aa"
                    ]
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetObject",
                "s3:GetObjectTagging"
            ],
            "Resource": [
                "arn:aws:s3:::bucket-app-prefix-*",
                "arn:aws:s3:::bucket-svc-fullname"
            ],
            "Condition": {
                "ForAllValues:StringEquals": {
                    "aws:SourceVpc": [
                        "vpc-857abc857abc875aa"
                    ]
                }
            }
        }
    ]
}
```

#### S3 Bucket Level Cross Account Policy
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::857857857857:root",
                    "arn:aws:iam::233233233233:root"
                ]
            },
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::BUCKET_NAME",
                "arn:aws:s3:::BUCKET_NAME/*"
            ]
        }
    ]
}
```
