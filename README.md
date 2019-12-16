## Deploying spring boot java application and MySql on Amazon EKS environment

Be familiar with Kubernetes architecture and have a look Amazon EKS pricing page.https://aws.amazon.com/eks/pricing/
In Short, you will be billed for Amazon EKS cluster as master node $0.20 per hour apart from worker nodes. For worker nodes according to EC2 pricing you will be charged
In our deployment, we will run EKS on AWS using either EC2.

Prerequisities 

Get a Docker account/id.
Download and install Docker Desktop
Enable Kubernetes from Preferences / Kubernate tab.
Make sure that Docker Desktop runs on your local environment

Get a github account.
Download and install git.
Follow this tutorial to login github from ssh-terminal 
https://kbroman.org/github_tutorial/pages/first_time.html
Make sure that you managed to log in to github from your terminal 

Install AWS CLI, eksctl.
As starting point, please follow Amazon EKS user guide to install AWS CLI and eksctl as explained
(https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html)

Create Administrator User and Group
Before configuring Your AWS CLI Credentials, you need to create IAM Admin User and Group as explained. Having Admin user will simplfy the EKS creation and deployment steps without drilling down to Amazon IAM user, role and policy structure. 
Follow this tutorial https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html
You should be able to create Administrator User smoohtly if you follow exactly the steps on above link.

Once you managed to create Administrator user, continue Configure Your AWS CLI Credentials as explained.

Clone sample project, update Maven pom.xml by adding 

Build your java project and do containerizing application to Docker daemon.
