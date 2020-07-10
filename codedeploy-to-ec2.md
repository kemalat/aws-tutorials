## Deployment Automation from S3 to EC2 instance

We will automate application deployment to Amazon EC2 instances using AWS CodeDeploy. This tutorial is actually available on AWS documentation but there are some details related IAM instance profile and policies that make the flow work are spread to different tutorials, our aim to collect to them one place for easy and fast spin up to process. 

### Prerequisities 
It is assumed that you have already provisioned IAM user, installed AWS CLI and you are already comfortable using CLI and AWS Console. Having IAM Admin User and Group for Administrator access to makes things easer. Follow this [tutorial](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) You should be able to create Administrator user smoohtly if you follow exactly the steps on above link using AWS web console. 

### 1. Create a Service Role

Follow the [this](https://docs.aws.amazon.com/codedeploy/latest/userguide/getting-started-create-service-role.html) till step 8. On the Create role page, choose AWS service, and from the Choose the service that will use this role list, choose CodeDeploy.

<img src="https://lh3.googleusercontent.com/pw/ACtC-3dMMyP10CIU1uUG42hGS_Y4ZV_xqd7Jtwh7SKtZxWPuPMlshYad9UCteGskuRUrbIsurlOLuMbLvGP-Zn5aHLVBPDv157U__EkZqPk2plLqJVAGB3tiTdFClLC5p3AcC-bZV57uU5gs8jVehzBKQBnI=w1998-h902-no?authuser=0" alt="drawing" width="500"/>

### 2. Create an IAM Instance Profile for EC2 Instances

Follow the [this](https://docs.aws.amazon.com/codedeploy/latest/userguide/getting-started-create-iam-instance-profile.html). 

<img src="https://lh3.googleusercontent.com/pw/ACtC-3fzKYz6ruCEze53NAJ0qGyJuEkG5SkfD7J_NAAV8NFvI9VJ0BREXdbZM1TRg_AQ6XPs3bPgcHarJ3VWGmEDlYPNjDldjFgmhA1Cqo15n1q5q9BRVz4c6KPLFnnejST4F0E88bdm_ansEcWq0tnJit8D=w1988-h1002-no?authuser=0" alt="drawing" width="500"/>

On the Create policy page, paste the following in the JSON tab:

<img src="https://lh3.googleusercontent.com/pw/ACtC-3enNiG5Z8LVOOSe9hCMEFn0whyBZgzBd_GAlMIbf4bTlznm7xzV-ZeDATG6-uZpoV2yrOPdnOuhMtT2KQqspYoC1F114QTZQ_c6r7k3XeK2HQmt50B5-bouHax7dmIc8WnLzUChFCZr7NBBY0dGN9fe=w1966-h1012-no?authuser=0" alt="drawing" width="500"/>





