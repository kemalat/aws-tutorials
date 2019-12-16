## Deploying spring boot java application and MySql on Amazon EKS environment
Prerequisities 
Get a Docker account/id.
Download and install Docker Desktop.(Enable Kubernetes)
Get a github account.
Download and install git.
https://kbroman.org/github_tutorial/pages/first_time.html


As starting point, please follow Amazon EKS user guide to install AWS CLI and eksctl as explained
(https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html)

Before configuring Your AWS CLI Credentials, you need to create IAM Admin User and Group as explained. Having Admin user will simplfy the EKS creation and deployment steps without drilling down to Amazon IAM user, role and policy structure. 
https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html
