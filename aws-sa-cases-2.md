### AWS AppSync Integration scheme and use cases

Sample use case : Web application allows user to post comment and receieve near real-time update about posts. 


<img src="https://d1.awsstatic.com/AppSync/product-page-diagram_AppSync@2x.d46d96d1e27169aa5005223299068da899280538.png" alt="drawing" width="500"/>

### How can the Developer enable the scaling for ECS service which should scale based on user load in the previous 20 seconds
Create a high-resolution custom Amazon CloudWatch metric for user activity data, then publish data every 10 seconds” is the correct answer
A custom metric can be one of the following:

- Standard resolution, with data having one-minute granularity
- High resolution, with data at a granularity of one second

### Propose solution for the “customers” microservice to get near real-time updates from the “orders” table on DynamoDB?

A company runs multiple microservices that each use their own Amazon DynamoDB table. 
The “customers” microservice needs data that originates in the “orders” microservice.

1-  Enable DynamoDB streams for the “orders” table, trigger an AWS Lambda function to read records from the stream

2 - Enable Amazon DynamoDB streams on the “orders” table, configure the “customers” microservice to read records from the stream

### Which service should the developer use to enable users to sign-up for the web application?

A user pool is a user directory in Amazon Cognito. With a user pool, users can sign in to your web or mobile app through Amazon Cognito. 
Users can also sign in through social identity providers like Google, Facebook, Amazon, or Apple, and through SAML identity providers.
Whether yusers sign in directly or through a third party, all members of the user pool have a directory profile that you can access through a Software Development Kit (SDK).

### What is the difference between an IPSec and an SSL VPN?

Internet Protocol Security (IPsec) is the traditional VPN method. Introduced in the 1990s, it is well established, 
regularly updated, and continues to be widely used. IPsec requires third-party client software on the user’s device to access the VPN—it is not implemented 
through the web browser. Secure Sockets Layer (SSL) is IPsec’s major rival as a VPN protocol. Though its origins also trace to the 1990s, 
SSL is a more recent method for implementing VPNs, and it is becoming increasingly popular. The SSL protocol was replaced by a successor technology, 
Transport Layer Security (TLS), in 2015, but the terms are interchangeable in common parlance and “SSL” is still widely used.
SSL VPNs are implemented through the remote user’s web browser and do not require the installation of special software. 

### AWS Storage Gateway , Cached Volume vs Stored Volume Architecture 

By using stored volumes, you can store your primary data locally, while asynchronously backing up that data to AWS. 
Stored volumes provide your on-premises applications with low-latency access to their entire datasets. At the same time, they provide durable, offsite backups. 
You can create storage volumes and mount them as iSCSI devices from your on-premises application servers. 
Data written to your stored volumes is stored on your on-premises storage hardware. 
This data is asynchronously backed up to Amazon S3 as Amazon Elastic Block Store (Amazon EBS) snapshots.

By using cached volumes, you can use Amazon S3 as your primary data storage, while retaining frequently accessed data locally in your storage gateway. 
Cached volumes minimize the need to scale your on-premises storage infrastructure, while still providing your applications with low-latency access to their 
frequently accessed data. You can create storage volumes up to 32 TiB in size and attach to them as iSCSI devices from your on-premises application servers. 
Your gateway stores data that you write to these volumes in Amazon S3 and retains recently read data in your on-premises storage gateway's cache and 
upload buffer storage


### Lambda function that will process events from a DynamoDB stream. 
Q. A company is setting up a Lambda function that will process events from a DynamoDB stream. The Lambda function has been created and a stream has been enabled. What else needs to be done for this solution to work?
A. An event-source mapping must be created on the Lambda side to associate the DynamoDB stream with the Lambda function

### Which services that Lambda Reads Events From

- Amazon Kinesis
- Amazon DynamoDB
- Amazon Simple Queue Service

An event source mapping uses permissions in the function’s execution role to read and manage items in the event source. 
The configuration of the event source mapping for stream-based services (DynamoDB, Kinesis), and Amazon SQS, is made on the Lambda side.

### Apache Hadoop on Amazon EMR use cases

- Clickstream analysis
- Log processing
- Petabyte scale analytics
 -Genomics
- ETL

### AWS CodeDeploy Blue/Green vs In-place deployments

All AWS Lambda and Amazon ECS deployments are blue/green. An EC2/On-Premises deployment can be in-place or blue/green. A blue/green deployment offers a number of advantages over an in-place deployment:

With a blue/green deployment, you provision a new set of instances on which CodeDeploy installs the latest version of your application. 
CodeDeploy then reroutes load balancer traffic from an existing set of instances running the previous version of your application to the new set of 
instances running the latest version. After traffic is rerouted to the new instances, the existing instances can be terminated. 

See in-place deployment steps

<img src="https://docs.aws.amazon.com/codedeploy/latest/userguide/images/sds_architecture.png" alt="drawing" width="500"/>


First, you create deployable content on your local development machine or similar environment, 
Next, you provide CodeDeploy with information about your deployment, 
such as which Amazon S3 bucket or GitHub repository to pull the revision from and to which set of Amazon EC2 instances to deploy its contents.Next, the CodeDeploy agent on each instance polls CodeDeploy to determine what and when to pull from the specified Amazon S3 bucket or GitHub repository.
Finally, the CodeDeploy agent on each instance pulls the target revision from the Amazon S3 bucket or GitHub repository and, using the instructions in the AppSpec file, deploys the contents to the instance.

### Elasticsearch vs. CloudSearch

Amazon CloudSearch is a fully managed service in the cloud that makes it easy to set up, manage, and scale a search solution for your website or application. 
With Amazon CloudSearch you can search large collections of data such as web pages, document files, forum posts, or product information. 

- Full text search with language-specific text processing

- Boolean search

- Prefix searches

- Range searches

- Term boosting

- Faceting

- Highlighting

- Autocomplete Suggestions

### Referance Architecture : Searching CloudTrail Logs Easily with Amazon CloudSearch

<img src="https://d2908q01vomqb2.cloudfront.net/cb4e5208b4cd87268b208e49452ed6e89a68e0b8/2016/10/28/Cloudtrail1.jpg" alt="drawing" width="500"/>

### You don't want to create new IAM users but make those users sign in to the AWS Management Console?

Use your on-premises SAML 2.0-compliant identity provider (IDP) to grant the NOC members federated access to the AWS Management 
Console via the AWS single sign-on (SSO) endpoint.

<img src="https://docs.aws.amazon.com/IAM/latest/UserGuide/images/saml-based-sso-to-console.diagram.png" alt="drawing" width="500"/>

### What is the main difference between Cognito user pool and Cognito identity pool?

User pools are for authentication (identify verification). With a user pool, your app users can sign in through the user pool or federate through a third-party identity provider (IdP). Identity pools are for authorization (access control).

### AWS Global Accelerator
AWS Global Accelerator is a networking service that sends your user's traffic through Amazon Web Service's global network infrastructure, improving your internet user performance by up to 60%.

### VPC Flow Logs
VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC. Flow log data can be published to Amazon CloudWatch Logs or Amazon S3.

###Where are AWS Cost and Usage Reports stored?
On S3 Buckets

### Which of the following does the AWS Trusted Adviser service offer advice? 

