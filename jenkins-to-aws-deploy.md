## Deployment to EC2 Auto Scaling Group by means of Jenkins and AWS CodeDeploy

We will use Jenkins plugin to deploy our builds automatically with AWS CodeDeploy. Our tasks will be;
* Create AWS CodeCommit repository
* Add files to the CodeCommit repository
* Install Jenkins and the Jenkins plugin
* Configure the plugin to create a deployment when changes are committed to an AWS CodeCommit repository.

### Notes 

### Prerequisities 
* Git client should be installed on your local working environment. 


### 1. Create Administrator User and Group
You need to create IAM Admin User and Group for Administrator access. Follow this [tutorial](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html)
You should be able to create Administrator user smoohtly if you follow exactly the steps on above link using AWS web console. 
Once you create Administrator user, we can continue with [Setup for HTTPS Users Using Git Credentials](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html)

### 2. Generate Access Keys
Access Keys are neede to provided programatic access EC2 instances and resources during the cloud formation proces. You should generate Access Keys for non-root Adminstrator user. 
* Log in to your AWS Management Console.
* Click on your user name at the top right of the page.
* Click on the Security Credentials link from the drop-down menu.
* Find the Access Credentials section, and copy the latest Access Key ID.
* If you do not know the secret key , you should new Access Key and note the Key ID and Secret Key

### 3. Setup for HTTPS Users Using Git Credentials
Follow the [tutorial](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html) till the Step 4 and then switch the AWS CodeCommit Web Console to crete repository. In order to login AWS CodeCommit Git Repository we need to have HTTPS Clone URL for the our remote repo.

### 4. Create AWS CodeCommit repository
Follow the [tutorial](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-migrate-repository-existing.html#how-to-migrate-existing-create). You need to follow up Step 1: Create a CodeCommit Repository and Step 2: Clone the Repository and Push to the CodeCommit Repository. 

### 5. Add existing project to remote Git Repository
The first time you connect, you are prompted for the user name and password for the repository. Depending on the configuration of your local computer. You can follow this [tutorial](https://help.github.com/en/github/importing-your-projects-to-github/adding-an-existing-project-to-github-using-the-command-line) for pushing the your existing project to remote repo

```
$ cd sample-app
$ git init
$ git add .
$ git commit -m "First commit"
[master (root-commit) 3a135e6] First commit
 13 files changed, 157 insertions(+)
 create mode 100644 README.md
 create mode 100755 appspec.yml
 create mode 100755 buildspec.yml
 create mode 100755 requirements.txt
 create mode 100755 scripts/mkdir.sh
 ....
$ git remote add origin https://git-codecommit.us-east-1.amazonaws.com/v1/repos/sample-app
# Sets the new remote
$ git remote -v
# Verifies the new remote URL
Push the changes in your local repository to GitHub.
$ git push -u origin master
# Pushes the changes in your local repository up to the remote repositor
```

### 6. Create a Jenkins server and AWS CodeDeploy environment using AWS CloudFormation
We will use one of sample CloudFormation template to create required stack for our pipeline. The link for initiating cloud formation :

[Launch Stack](https://eu-central-1.console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/quickcreate?templateURL=https://blog-for-codedeploy.s3.eu-central-1.amazonaws.com/CodeDeployTemplate.json) 

You can also [download](https://blog-for-codedeploy.s3.eu-central-1.amazonaws.com/CodeDeployTemplate.json) the AWS Clouformation template. Data strucutre details of JSON should be another topic since it requires diving the AWS Cloud Platform in details. 

### 6. Follow Amazon tutorial from this point
Now you are ready to execute directives listed on this Amazon [tutorial](https://aws.amazon.com/blogs/devops/setting-up-a-ci-cd-pipeline-by-integrating-jenkins-with-aws-codebuild-and-aws-codedeploy/)




