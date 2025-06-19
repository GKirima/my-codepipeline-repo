## STEP ONE
# CREATE AMAZON EC2 LINUX INSTANCES
# create an instance role in IAM, if you have not already created an instance role in the Region where you want to create resources.

# To create an instance role
<!-- 1. Open the IAM console at https://console.aws.amazon.com/iam/).

2. From the console dashboard, choose Roles.

3. Choose Create role.

4. Under Select type of trusted entity, select AWS service. Under Choose a use case, select EC2. Under Select your use case, choose EC2. Choose Next: Permissions.

5. Search for and select the policy named AWSSystemsManagerDefaultEC2InstanceManagementRoleeployAction.

6. Search for and select the policy named AmazonSSMManagedInstanceCore. Choose Next: Tags.

7. Choose Next: Review. Enter a name for the role (for example, EC2InstanceRole).
8. Choose Create role.  -->
# NOTE: You will add permissions to this role to allow access to the S3 artifact bucket for your pipeline after pipeline creation.

# To launch instances
<!-- 1. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.

2. From the side navigation, choose Instances, and select Launch instances from the top of the page.

3. In Name, enter MyInstances. This assigns the instance a tag Key of Name and a tag Value of MyInstances.

4. Under Application and OS Images (Amazon Machine Image), locate the Amazon Linux AMI option with the AWS logo, and make sure it is selected. (This AMI is described as the Amazon Linux 2 AMI (HVM) and is labeled "Free tier eligible".)

5. Under Instance type, choose the free tier eligible t2.micro type as the hardware configuration for your instance.

6. Under Key pair (login), choose a key pair or create one.

7. Under Network settings, make sure the status is Enable.

8. Expand Advanced details. In IAM instance profile, choose the IAM role you created in the previous procedure (for example, EC2InstanceRole).
9. Under Summary, under Number of instances, enter 2.

10. Choose Launch instance.

11. You can view the status of the launch on the Instances page. When you launch an instance, its initial state is pending. After the instance starts, its state changes to running, and it receives a public DNS name. (If the Public DNS column is not displayed, choose the Show/Hide icon, and then select Public DNS.) -->

## STEP TWO
## Create and Add a script file to your repository

create a repo in github and a script.sh file for the post-script step in the deployment.
touch script.sh
chmod +x script.sh  # make it executable

# Add your commands to it, for example:
#!/bin/bash

echo "Running post-deployment tasks..."

# Example commands:
# systemctl restart myapp.service
# npm install
# python3 manage.py migrate
# echo "Deployment complete!" >> /var/log/deploy.log

# Save and commit:
git add script.sh
git commit -m "Add post-deployment script"
git push origin main


# If Using AWS CodeDeploy — Include appspec.yml
You must define the script.sh in appspec.yml like so:

<!-- version: 0.0
os: linux
files:
  - source: /
    destination: /home/ec2-user/myapp

hooks:
  AfterInstall:
    - location: script.sh
      timeout: 300
      runas: ec2-user -->

When you deploy an application using AWS CodeDeploy, you need to include a file called appspec.yml in the root of your application repository. This file tells AWS how to deploy your app — including what scripts to run and when to run them during the deployment process.

#  to tell AWS in the appspec.yml file that it should run your script.sh file during a specific phase of the deployment — for example, after copying the app files to the server (AfterInstall hook).

## How to create the appspec.yml
<!-- Using Git Bash or VS Code Terminal
Navigate to your project folder:


cd path/to/your-repo
Create the file:

touch appspec.yml

Open it in VS Code:

code appspec.yml
Paste the following sample content:

version: 0.0
os: linux

files:
  - source: /
    destination: /home/ec2-user/myapp

hooks:
  AfterInstall:
    - location: script.sh
      timeout: 300
      runas: ec2-user -->

## commit and push:

git add appspec.yml
git commit -m "Add appspec.yml for AWS CodeDeploy"
git push origin main