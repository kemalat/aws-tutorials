## HA architecture alternatives for VPC

1. Consider putting all servers behind ELB. Configure a Route53 CNAME to point to the ELB DNS name.
<img src="https://campus.barracuda.com/resources/attachments/image/53248247/2/multi_AZ_routeshifting_ha_0.png" alt="drawing" width="500"/>

2. Assigning EIPs to all web servers. Configure a Route53 record set with all EIPs, with DNS health checks to catch DNS failover.

<img src="https://i.ytimg.com/vi/dlaWYedWnb0/maxresdefault.jpg" alt="drawing" width="400"/>


## Downstream data processing with Amazon Kinesis and RedShift EMR 

Real-time analyzing the inbound data in parallel and persisting result of the analytic processing for data mining

<img src="https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2019/06/05/DownstreamDataFirehoseEMRSpark1.png" alt="drawing" width="500"/>

### Multi-tier web application referance architecture
It runs in a VPC that uses ELBs in front of both the web and the app tier with static assets served directly from S3. 
At database layer, using the  combination of RDS and DynamoDB for dynamic data and then archiving nightly into S3 for further processing with EMR.

<img src="https://i.pinimg.com/originals/28/c6/21/28c6215374a9fb686973fbb6fccd2a3f.png" alt="drawing" width="500"/>


###  Scalable mitigation architecture for unauthorized access to VPC

<img src="https://image.slidesharecdn.com/awsaugustwebinarseries-ddosresiliency-08202015-150901224744-lva1-app6891/95/aws-august-webinar-series-ddos-resiliency-12-638.jpg?cb=1441147763" alt="drawing" width="500"/>

###  On-premises backup paths to AWS Cloud
Migration path for file, volume and tape backups to AWS

<img src="https://d2908q01vomqb2.cloudfront.net/e1822db470e60d090affd0956d743cb0e7cdf113/2019/11/23/Use-case-1-More-on-premises-backups-to-the-cloud.png" alt="drawing" width="500"/>

###  Several encyrption methods for the data on S3

1. Use Amazon S3 server-side encryption with AWS Key Management Service managed keys.
2. Use Amazon S3 server-side encryption with customer-provided keys.
3. Encrypt the data on the client-side before ingesting to Amazon S3 using their own master key.

<img src="https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2018/03/07/Rajat-featured-image-800-by-400.png" alt="drawing" width="500"/>

### Global Accelerator use cases

Global Accelerator is a good fit for non-HTTP use cases, such as gaming (UDP), IoT (MQTT), or Voice over IP, as well as for HTTP use cases that 
specifically require static IP addresses or deterministic, fast regional failover. Both services integrate with AWS Shield for DDoS protection.

<img src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2019/12/10/autoscaling_lambda_globalaccelerator-1024x312.png" alt="drawing" width="500"/>

### Protection against DDoS attacks(AWS Shield, CloudFront and Route 53)

<img src="https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2017/03/15/HW3-DDoS-a.png" alt="drawing" width="500"/>

### Placement group types
- Cluster – packs instances close together inside an Availability Zone. This strategy enables workloads to achieve the low-latency network performance necessary for tightly-coupled node-to-node communication that is typical of HPC applications.
- Partition – spreads your instances across logical partitions such that groups of instances in one partition do not share the underlying hardware with groups of instances in different partitions. This strategy is typically used by large distributed and replicated workloads, such as Hadoop, Cassandra, and Kafka.
- Spread – strictly places a small group of instances across distinct underlying hardware to reduce correlated failures.

### ENI vs EFA

An elastic network interface is a logical networking component in a VPC that represents a virtual network card. It can include the following attributes: A primary private IPv4 address from the IPv4 address range of your VPC.
Elastic Fabric Adapter (EFA) is a network interface for Amazon EC2 instances that enables customers to run applications requiring high levels of inter-node communications at scale on AWS.


### AWS S3 consistency model

- Amazon S3 offers eventual consistency for overwrite PUTS and DELETES in all Regions.
- Amazon S3 provides read-after-write consistency for PUTS of new objects in your S3 bucket in all Regions

### EBS volumes

- Single EBS volume can’t be attached to multiple EC2 instances.
- Multiple EBS volumes can be attached to a single EC2 instance.

### What is user data in AWS?

When you launch an instance in Amazon EC2, you have the option of passing user data to the instance that can be used to perform common automated 
configuration tasks and even run scripts after the instance starts. You can pass two types of user data to Amazon EC2: shell scripts and cloud-init directives.

### Route 53 A Record 
Currently, the zone apex, “example.com” points directly to the web server IP address. The IP address must be changed. 
Which Route 53 record type can be modified to accomplish this task?

### ELB Access Log Content
Elastic Load Balancing provides access logs that capture detailed information about requests sent to your load balancer. 
Each log contains request times, client IP addresses, latencies, request paths, and server responses.

### In-memory engines that can be used ElastiCache
- Redis and Memcached

###  is AMI source region dependent?
AMI source data resides in one particular region, AWS allows you to use it in only that same region. 
If you want to copy that AMI data to another region, you have to do so explicitly. 
Amazon keeps regional data within the region and do not share it with other region. 

###  IAM user permissions are region dependent?
No region is required to be specified when you define IAM user permissions. 
Users can use AWS services in any geographic region.

###  When you call the database tier from your app tier instances, you receive a timeout error, what can be the reason ?

Architecture : You have a three-tier web application (web, app, and data) in a single Amazon VPC. The web and app
tiers each span two Availability Zones, are in separate subnets, and sit behind ELB Classic Load
Balancers. The data tier is a Multi-AZ Amazon RDS MySQL database instance in database subnets. When
you call the database tier from your app tier instances, you receive a timeout error.

Cause : The security group for the Amazon RDS instance does not allow traffic on port 3306 from the app
instances.

– Security groups block all network traffic by default, so if a group is not correctly configured, it can lead to a
timeout error. MySQL security, not IAM, controls MySQL security. All subnets in an Amazon VPC have routes to
all other subnets. Internal traffic within an Amazon VPC does not require public IP addresses.

### AWS Secrets Manager use case

Database will be migrated to AWS RDS for MySQL , propose solution to store and automatically rotate MySQL database credentials. 

Solution : Storing MySQL database credentials in AWS Secrets Manager and configure it to automatically rotate

AWS Secrets Manager helps you protect secrets needed to access your applications, services, and IT resources. 
The service enables you to easily rotate, manage, and retrieve database credentials, API keys, and other secrets throughout their lifecycle.


###  How can you securely provide credentials that allow your cloud application on AWS to write to the queue?

 Launch the application's Amazon EC2 instance with an IAM role. IAM roles are based on temporary security tokens, so they are rotated automatically. 

### Optimistic concurrency vs Pessimistic concurrency

where you read a record, take note of a version number (other methods to do this involve dates, timestamps or checksums/hashes) and 
check that the version hasn't changed before you write the record back. When you write the record back you filter the update on the version to make sure 
it's atomic. (i.e. hasn't been updated between when you check the version and write the record to the disk) and update the version in one hit. If the record is dirty (i.e. different version to yours) you abort the transaction and the user can re-start it.

Pessimistic is when you lock the record for your exclusive use until you have finished with it. 
It has much better integrity than optimistic locking but requires you to be careful with your application design to avoid Deadlocks. 

### Optimistic concurrency vs Pessimistic concurrency use case on DynamoDb
Your web application reads an item from your DynamoDB table, changes an attribute, and then writes
the item back to the table. You need to ensure that one process doesn't overwrite a simultaneous change
from another process.
- Implement optimistic concurrency by using a conditional write.

Optimistic concurrency depends on checking a value upon save to ensure that it has not changed.
Pessimistic concurrency prevents a value from changing by locking the item or row in the database. DynamoDB
does not support item locking, and conditional writes are perfect for implementing optimistic concurrency.

### What happens to data stored on an instance store volume when an ec2 instance is stopped or shutdown?

Instance store volume are ephemeral and are automatically wiped when an instance stops or terminates. 
EBS volumes are persistent and retain their data when an instance is stopped and started.

### What DR strategy could be used to achieve this Recovery Time Objective < 3h and Recovery Point Objective = 15 min in the event of this kind of failure?

Take hourly DB backups to S3, with transaction logs stored in S3 every 5 minutes.

