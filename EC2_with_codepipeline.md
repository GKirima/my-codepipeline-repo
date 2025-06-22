## STEP ONE<br>
### CREATE AMAZON EC2 LINUX INSTANCES<br>
#### Create an instance role in IAM, if you have not already created an instance role in the Region where you want to create resources.<br>

#### A. To create an instance role<br>
1. Open the IAM console at https://console.aws.amazon.com/iam/).<br>

2. From the console dashboard, choose Roles.<br>

3. Choose Create role.<br>

4. Under Select type of trusted entity, select AWS service. Under Choose a use case, select EC2. Under Select your use case, choose EC2. Choose Next: Permissions.<br>

5. Search for and select the policy named<br> AWSSystemsManagerDefaultEC2InstanceManagementRoleeployAction.<br>

6. Search for and select the policy named AmazonSSMManagedInstanceCore.<br> 
Choose Next:<br> 
Tags.<br>

7. Choose Next: Review. Enter a name for the role (for example, EC2InstanceRole).<br>
8. Choose Create role.<br>
### NOTE: You will add permissions to this role to allow access to the S3 artifact bucket for your pipeline after pipeline creation.<br>

#### B. To launch instances<br>
 1. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.<br>

2. From the side navigation, choose Instances, and select Launch instances from the top of the page.<br>

3. In Name, enter MyInstances. This assigns the instance a tag Key of Name and a tag Value of MyInstances.<br>

4. Under Application and OS Images (Amazon Machine Image), locate the Amazon Linux AMI option with the AWS logo, and make sure it is selected. (This AMI is described as the Amazon Linux 2 AMI (HVM) and is labeled "Free tier eligible".)<br>

5. Under Instance type, choose the free tier eligible t2.micro type as the hardware configuration for your instance.<br>

6. Under Key pair (login), choose a key pair or create one.<br>

7. Under Network settings, make sure the status is Enable.<br>

8. Expand Advanced details. In IAM instance profile, choose the IAM role you created in the previous procedure (for example, EC2InstanceRole).<br>
9. Under Summary, under Number of instances, enter 2.<br>

10. Choose Launch instance.<br>

11. You can view the status of the launch on the Instances page.<br> 
- When you launch an instance, its initial state is pending.<br> 
- After the instance starts, its state changes to running, and it receives a public DNS name.(If the Public DNS column is not displayed, choose the Show/Hide icon, and then select Public DNS.)<br>

## STEP TWO<br>

### Create and Add a script file to your repository<br>

create a repo in github and a script.sh file for the post-script step in the deployment.<br>
- touch script.sh<br>
- chmod +x script.sh  # make it executable<br>

#### Add your commands to it, for example:<br>
- *#!/bin/bash*<br>

- *echo "Running post-deployment tasks..."*<br>

### Example commands:<br>
- *systemctl restart myapp.service*<br>
- *npm install*<br>
- *python3 manage.py migrate*<br>
- *echo "Deployment complete!" >> /var/log/deploy.log*<br>

### Save and commit:<br>
- *git add script.sh*<br>
- *git commit -m "Add post-deployment script"*<br>
- *git push origin main*<br>


### If Using AWS CodeDeploy — Include appspec.yml<br>
You must define the script.sh in appspec.yml like so:<br>
```yaml
version: 0.0
os: linux
files:
  - source: /
    destination: /home/ec2-user/myapp

hooks:
  AfterInstall:
    - location: script.sh
      timeout: 300
      runas: ec2-user
```
When you deploy an application using AWS CodeDeploy, you need to include a file called appspec.yml in the root of your application repository.<br> This file tells AWS how to deploy your app — including what scripts to run and when to run them during the deployment process.<br>

- to tell AWS in the appspec.yml file that it should run your script.sh file during a specific phase of the deployment — for example, after copying the app files to the server (AfterInstall hook).<br>

## How to create the appspec.yml<br>
 Using Git Bash or VS Code Terminal<br>
Navigate to your project folder:<br>


cd path/to/your-repo<br>
Create the file:<br>

touch appspec.yml<br>

Open it in VS Code:<br>

code appspec.yml<br>
Paste the following sample content:<br>

```yaml
version: 0.0
os: linux
files:
  - source: /
    destination: /home/ec2-user/myapp

hooks:
  AfterInstall:
    - location: script.sh
      timeout: 300
      runas: ec2-user
```

## commit and push:<br>

- *git add appspec.yml*<br>
- *git commit -m "Add appspec.yml for AWS CodeDeploy"*<br>
- *git push origin main*<br>

## STEP THREE<br>
### Add artifact bucket permissions to the EC2 instance role
You must update the EC2 instance role you created for your instance to allow it access to your pipeline's artifact bucket. 

### Note

When you create the instance, you create or use an existing EC2 instance role. To avoid *Access Denied* errors, you must add S3 bucket permissions to the instance role to give the instance permissions to the CodePipeline artifact bucket.<br> Create a default role or update your existing role with the *s3:GetObject* permission scoped down to the artifact bucket for your pipeline's Region. 

- 

1. Navigate to your pipeline in the CodePipeline console. Choose Settings. View the name and location of the artifact store for an existing pipeline.<br> Make a note of the artifact bucket Amazon Resource Name (ARN) and copy it.

2. Navigate to the IAM console and choose Roles. Choose the instance role you created in Step 1 of this tutorial.

3. On the Permissions tab, choose Add inline policy.

3. Add the following JSON to the policy document, replacing the value in the Resource field with the bucket ARN.

```json
{
    "Effect": "Allow",
    "Principal": "*",
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::*BucketName*"
}
```
5. Choose Update.

## STEP FOUR<br>
### Creating your pipeline
Use the CodePipeline wizard to create your pipeline stages and connect your source repository.
### To create your pipeline
1. Open the CodePipeline console at https://console.aws.amazon.com/codepipeline/

2. On the Welcome page, Getting started page, or the Pipelines page, choose Create pipeline.

3. On the Step 1: Choose creation option page, under Creation options, choose the Build custom pipeline option. Choose Next.

4. In Step 2: Choose pipeline settings, in Pipeline name, enter MyPipeline.

5. CodePipeline provides V1 and V2 type pipelines, which differ in characteristics and price. The V2 type is the only type you can choose in the console. For more information, see pipeline types. For information about pricing for CodePipeline, see Pricing

6. In Service role, choose Use existing service role, and then choose the CodePipeline service role that has been updated with the required permissions for this action. To configure your CodePipeline service role for this action, see Service role policy permissions for the EC2 deploy action.

7. Leave the settings under Advanced settings at their defaults, and then choose Next.

8. On the Step 3: Add source stage page, add a source stage:

    a. In Source provider, choose GitHub (via GitHub App).

    b. Under Connection, choose an existing connection or create a new one. To create or manage a connection for your GitHub source action, see GitHub connections.

    c. In Repository name, choose the name of your GitHub repository.

Choose Next.

9. On the Step 4: Add build stage page, choose Skip.

10. On the Step 5: Add deploy stage page, choose EC2.
  - a. For Target directory, enter the directory on the instance that you want to deploy to, such as /home/ec2-user/testhelloworld.
### Note

  - Specify the deployment directory that you want the action to use on the instance.<BR> The action will automate creating the specified directory on the instance as part of the deployment. 
  - b. For PostScript, enter the path and file name for your script, such as test/script.sh.
  - c. Choose Next.
11. On the Step 6: Review page, review your pipeline configuration and choose Create pipeline to create the pipeline.
12. After the pipeline runs successfully, choose View details to view the logs on the action to view the managed compute action output.

## STEP FIVE 
### Test Your Pipeline

Your pipeline should have everything for running an end-to-end native AWS continuous deployment. Now, test its functionality by pushing a code change to your source repository.
### To test your pipeline

1. Make a code change to your configured source repository, commit, and push the change.

2. Open the CodePipeline console at https://console.aws.amazon.com/codepipeline/

3. Choose your pipeline from the list.

4. Watch the pipeline progress through its stages. Your pipeline should complete and your action deploys the script on your instances.

5. For more troubleshooting information, see EC2 Deploy action fails with an error message No such file.
