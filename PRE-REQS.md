

## Pre-Requisites

In order to build a custom kernel your Amazon SageMaker Studio Execution role will require additional permissions to 

1. Run a AWS CodeBuild project
2. Build and register a docker container with the Amazon Elastic Container Registry (ECR)
3. Update your SageMaker domain to attach the kernel image.

## Trust Relationship with AWS CodeBuild

The first is that the Sagemaker Execution Policy should have a trust policy with CodeBuild. So that it can execute the image build using CodeBuild.

Go to IAM and find your Sagemaker Execution Role. Then edit to the Trust Relationships to add the following (or something similar)

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": [
          "codebuild.amazonaws.com"
        ]
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

## AWS CodeBuild Permissions

Next you will need to add a policy to your Role so that you can use all of the required CodeBuild functions. Add something like the following as a policy to your Role.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "codebuild:DeleteProject",
                "codebuild:CreateProject",
                "codebuild:BatchGetBuilds",
                "codebuild:StartBuild"
            ],
            "Resource": "arn:aws:codebuild:*:*:project/sagemaker-studio*"
        },
        {
            "Effect": "Allow",
            "Action": "logs:CreateLogStream",
            "Resource": "arn:aws:logs:*:*:log-group:/aws/codebuild/sagemaker-studio*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "logs:GetLogEvents",
                "logs:PutLogEvents"
            ],
            "Resource": "arn:aws:logs:*:*:log-group:/aws/codebuild/sagemaker-studio*:log-stream:*"
        },
        {
            "Effect": "Allow",
            "Action": "logs:CreateLogGroup",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ecr:CreateRepository",
                "ecr:BatchGetImage",
                "ecr:CompleteLayerUpload",
                "ecr:DescribeImages",
                "ecr:DescribeRepositories",
                "ecr:UploadLayerPart",
                "ecr:ListImages",
                "ecr:InitiateLayerUpload",
                "ecr:BatchCheckLayerAvailability",
                "ecr:PutImage"
            ],
            "Resource": "arn:aws:ecr:*:*:repository/sagemaker-studio*"
        },
        {
            "Effect": "Allow",
            "Action": "ecr:GetAuthorizationToken",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
              "s3:GetObject",
              "s3:DeleteObject",
              "s3:PutObject"
              ],
            "Resource": "arn:aws:s3:::sagemaker-*/*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:CreateBucket"
            ],
            "Resource": "arn:aws:s3:::sagemaker*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "iam:GetRole",
                "iam:ListRoles"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "arn:aws:iam::*:role/*",
            "Condition": {
                "StringLikeIfExists": {
                    "iam:PassedToService": "codebuild.amazonaws.com"
                }
            }
        }
    ]
}
```

## Permissions to Modify Amazon Sagemaker Studio Domain

Finally you will need to add a policy to your Role that will allow you to modify the Studio domain. This is the final step where you will make your custom kernel available within Sagemaker Studio to run a Notebook.

Add the policy below (or something similar) to your Sagemaker Execution Role.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "sagemaker:CreateApp",
                "sagemaker:CreateAppImageConfig",
                "sagemaker:CreateDomain",
                "sagemaker:CreateImage",
                "sagemaker:CreateImageVersion",
                "sagemaker:UpdateDomain"
            ],
            "Resource": "*"
        }
    ]
}
```