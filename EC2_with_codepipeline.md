## STEP ONE<br>
### CREATE AMAZON EC2 LINUX INSTANCES<br>
#### create an instance role in IAM, if you have not already created an instance role in the Region where you want to create resources.<br>

#### To create an instance role<br>
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

#### To launch instances<br>
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
When you launch an instance, its initial state is pending.<br> 
After the instance starts, its state changes to running, and it receives a public DNS name.<br>
(If the Public DNS column is not displayed, choose the Show/Hide icon, and then select Public DNS.)<br>

## STEP TWO<br>
## Create and Add a script file to your repository<br>

create a repo in github and a script.sh file for the post-script step in the deployment.<br>
touch script.sh<br>
chmod +x script.sh  # make it executable<br>

#### Add your commands to it, for example:<br>
#!/bin/bash<br>

echo "Running post-deployment tasks..."<br>

### Example commands:<br>
- systemctl restart myapp.service<br>
- npm install<br>
- python3 manage.py migrate<br>
- echo "Deployment complete!" >> /var/log/deploy.log<br>

### Save and commit:<br>
- *git add script.sh*<br>
- *git commit -m "Add post-deployment script"*<br>
- *git push origin main*<br>


### If Using AWS CodeDeploy — Include appspec.yml<br>
You must define the script.sh in appspec.yml like so:<br>

 version: 0.0<br>
os: linux<br>
files:
  - source: /<br>
    destination: /home/ec2-user/myapp<br>

hooks:<br>
  AfterInstall:<br>
    - location: script.sh<br>
      timeout: 300<br>
      runas: ec2-user <br>

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

version: 0.0<br>
os: linux<br>

files:<br>
  - source: /<br>
    destination: /home/ec2-user/myapp<br>

hooks:<br>
  AfterInstall:<br>
    - location: script.sh<br>
      timeout: 300<br>
      runas: ec2-user<br>

## commit and push:<br>

- *git add appspec.yml*<br>
- *git commit -m "Add appspec.yml for AWS CodeDeploy"*<br>
- *git push origin main*<br>