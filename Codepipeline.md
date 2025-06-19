##  1. EC2 instance role
## Creating aws iam role using cli
aws iam create-role \
  --role-name CodeDeploy_EC2_Service_Role \
  --assume-role-policy-document file://trust-ec2.json
## Example trust-ec2.json:
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
## Then attach policies:
aws iam attach-role-policy \
  --role-name CodeDeploy_EC2_Service_Role \
  --policy-arn arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore

aws iam attach-role-policy \
  --role-name CodeDeploy_EC2_Service_Role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AmazonEC2RoleforAWSCodeDeploy

## 2. CodeDeploy service role
## This is used by CodeDeploy itself to execute deployments.

## Policy:
aws iam create-role \
  --role-name CodeDeployServiceRole \
  --assume-role-policy-document file://trust-codedeploy.json
## trust-codedeploy.json:
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "codedeploy.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
## Attach policy:
aws iam attach-role-policy \
  --role-name CodeDeployServiceRole \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSCodeDeployRole

## 3. CodePipeline role (optional)
# If you're using AWS CLI to build CodePipeline, you'll also need a role with:

# - AWSCodePipelineFullAccess

# - CodeDeploy, CodeBuild, S3, IAM, etc. permissions

# Role	                 Attached Policy	                      Purpose
# EC2 instance role	     AmazonSSMManagedInstanceCore, 	          Let EC2 be managed via 
#                        AmazonEC2RoleforAWSCodeDeploy            SSM and accept deployments

# CodeDeploy role	          AWSCodeDeployRole	                            Allow CodeDeploy to run deployment

# Pipeline role	         Custom or AWSCodePipelineFullAccess	       Allow orchestration across services