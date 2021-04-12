
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
