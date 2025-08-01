###  1. EC2 instance role
#### Creating aws iam role using cli<br>
- aws iam create-role \

- role-name CodeDeploy_EC2_Service_Role \

- assume-role-policy-document file://trust-ec2.json

#### Example trust-ec2.json:
<pre>
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
</pre>
### Then attach policies:
- aws iam attach-role-policy \
- role-name CodeDeploy_EC2_Service_Role \
- policy-arn arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore

- aws iam attach-role-policy \
- role-name CodeDeploy_EC2_Service_Role \
- policy-arn arn:aws:iam::aws:policy/service-role/AmazonEC2RoleforAWSCodeDeploy

### 2. CodeDeploy service role<br>
#### This is used by CodeDeploy itself to execute deployments.<br>

### Policy:<br>
- aws iam create-role \
- role-name CodeDeployServiceRole \
- assume-role-policy-document file://trust-codedeploy.json
### trust-codedeploy.json:<br>

<pre>

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
</pre>

### Attach policy:<br>
aws iam attach-role-policy \
role-name : CodeDeployServiceRole \
policy-arn : arn:aws:iam::aws:policy/service-role/AWSCodeDeployRole<br>

### 3. CodePipeline role (optional)<br>
- If you're using AWS CLI to build CodePipeline, you'll also need a role with:<br>

 - AWSCodePipelineFullAccess<br>

 - CodeDeploy, CodeBuild, S3, IAM, etc. permissions<br>

|    Role           |    Attached Policy             | Purpose<br>
|:-----------------------|---------------------------|---------------------------
|EC2 instance role |AmazonSSMManagedInstanceCore    |Let EC2 be managed via<br>
|                  |AmazonEC2RoleforAWSCodeDeploy    |SSM and accept deployments<br>
|CodeDeploy role   |AWSCodeDeployRole                |Allow CodeDeploy to run deployment<br>
|Pipeline role |Custom or AWSCodePipelineFullAccess|Allow orchestration across services<br>
|
	         	       