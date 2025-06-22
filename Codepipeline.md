##  1. EC2 instance role
#### Creating aws iam role using cli<br>
-- aws iam create-role \<br>
  --role-name CodeDeploy_EC2_Service_Role \<br>
  --assume-role-policy-document file://trust-ec2.json<br>
### Example trust-ec2.json:
{
  "Version": "2012-10-17",<br>
  "Statement": [<br>
    {
      "Effect": "Allow",<br>
      "Principal": {<br>
        "Service": "ec2.amazonaws.com"<br>
      },<br>
      "Action": "sts:AssumeRole"<br>
    }<br>
  ]<br>
}<br>
### Then attach policies:
aws iam attach-role-policy \<br>
  --role-name CodeDeploy_EC2_Service_Role \<br>
  --policy-arn arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore<br>

aws iam attach-role-policy \<br>
  --role-name CodeDeploy_EC2_Service_Role \<br>
  --policy-arn arn:aws:iam::aws:policy/service-role/AmazonEC2RoleforAWSCodeDeploy<br>

## 2. CodeDeploy service role<br>
## This is used by CodeDeploy itself to execute deployments.<br>

## Policy:<br>
aws iam create-role \<br>
  --role-name CodeDeployServiceRole \<br>
  --assume-role-policy-document file://trust-codedeploy.json<br>
## trust-codedeploy.json:<br>
{<br>
  "Version": "2012-10-17",<br>
  "Statement": [<br>
    {<br>
      "Effect": "Allow",<br>
      "Principal": {<br>
        "Service": "codedeploy.amazonaws.com"<br>
      },<br>
      "Action": "sts:AssumeRole"<br>
    }<br>
  ]<br>
}<br>
## Attach policy:<br>
aws iam attach-role-policy \<br>
  --role-name CodeDeployServiceRole \<br>
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSCodeDeployRole<br>

## 3. CodePipeline role (optional)<br>
- If you're using AWS CLI to build CodePipeline, you'll also need a role with:<br>

 - AWSCodePipelineFullAccess<br>

 - CodeDeploy, CodeBuild, S3, IAM, etc. permissions<br>

|    Role           |    Attached Policy             | Purpose<br>
|:-----------------------|---------------------------|---------------------------
|EC2 instance role |AmazonSSMManagedInstanceCore    |Let EC2 be managed via<br>
|                  |AmazonEC2RoleforAWSCodeDeploy    |SSM and accept deployments<br>

|CodeDeploy role    |AWSCodeDeployRole        |Allow CodeDeploy to run deployment<br>

|Pipeline role |Custom or AWSCodePipelineFullAccess|Allow orchestration across services<br>
	         	       