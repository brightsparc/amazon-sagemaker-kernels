# Bring your own SageMaker Studio Image

This repository provides you a quick start for
 [Bringing your own custom container image to Amazon SageMaker Studio notebooks](https://aws.amazon.com/blogs/machine-learning/bringing-your-own-custom-container-image-to-amazon-sagemaker-studio-notebooks/).

## Prerequisites

In order to build a custom kernel your Amazon SageMaker Studio Execution role will require additional permissions, see the [PRE-REQS.md](PRE-REQS.md) for applying to an existing role.

Alternatively, you use the AWS CloudFormation stack `cloudformation\sagemaker-studio-infra.yaml` to setup a new SageMaker Studio domain and Execution role with sufficient permissions, or click the following button to launch the stack in `us-west-1` region.

[![Launch CFN stack](https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png)](https://us-west-1.console.aws.amazon.com/cloudformation/home?region=us-west-1#/stacks/quickcreate?templateUrl=https%3A%2F%2Famazon-sagemaker-safe-deployment-pipeline.s3.amazonaws.com%2Fstudio-kernel%2Fsagemaker-studio-infra.yaml&stackName=sagemaker-studio-custom-kernel&param_SageMakerStudioDomainName=default)

This stack will return output parameters for the following resources
1. SageMaker Default S3 Bucket
2. SageMaker Studio Domain ID
3. SageMaker Studio Execution Role

## Build R Notebook Kernel for Sagemaker Studio

In the [r-kernel](r-kernel) example we demonstrate how to
use the Sagemaker Studio Image Build CLI to build and register
a customer Notebook Kernel from within Sagemaker Studio.

This allows you to run R code inside a Sagemaker Studio Notebook.