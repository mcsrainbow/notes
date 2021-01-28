### Q - A - K

* self-managed Oracle, snapshot 12 hours - B - minimum instance count of two

* three-tiers web, RDS, API Gateway, Lambda - B - On-Demand for web, Reserved instances for db

* S3, VPC, fears malicious - A - limit access to VPC endpoint

* * 4, serverless, 504 Gateway Timeout - B,D - Parse Amazon CloudWatch Logs, AWS X-Ray

* EC2, POSIX, 100k - C,E - EFS, Service layer Ephemeral

* jpg, S3, 15 to 20 days - A,B - Reserved instances, to S3 IA after 30 days

* hybrid network architecture, NAT cannot be used - A - Use AWS Direct Connec to each data center from different ISPs

* I/O intensive, 2TB gp2 volumes, IOPS by 3000 for each - B - increase gp2 volumes to 3TB

* * 10-Gbps AWS Direct Connect, 200 Mbps, 150 TB, Monday morning - D - Create a public virtual interface on a Direct Connect

* total of 2000 accounts, Docker - D - Create a Network Load Balancer (NLB)

* dynamic mission-critical, SLA of 99.99% - C - Route 53 latency-based routing

* 200 separate internet-facing web, FQDNs, HTTPS, ALB - D - Request certificates for each FQDN in both

* e-commerce, revamping its IT infrastructure, CIO - B - SQS, Lambda

* in-house software framwork - A - pipeline to build a custom AMI

* each business unit, complete autonomy - A - individual accounts for each, add to an OU

* to run on a serverless, SaaS provider - A - Lambda, NAT gateway, SaaS provider

* extremely sensitive to its IT costs - A,E,F - workload tag, Define workload, Set up AWS Budgets

* IBM WebShpere, IBM DB2, z/OS - B - Amazon MQ, EC2-based DB2

* uploads user photots to S3, DynamoDB - B,D - write capacity unites, a dead letter queue

* group of imaging satellites - A - multiple AWS Snowball, 1-Gb AWS Direct Connect, S3

* streaming markdet data, 1-year reservations, NFS - B - Spot market bid of 50%

* collect logs and send them to a centralized - C - Logs agent to stream log messages directly

* DynamoDB, must be logged within 30 minutes - C - DynamoDB Streams to capture and send updates

* All application, single AWS, different VPC - C - tagging policy based

* Simple PHP, Java, or Ruby - C - Convert to Docker, ECS

* backup solution, 500MB source, RPO of 1, RTO of 2 - B - Standard 0 Infrequent Access

* CFO, AWS Elastic Beanstalk - B - Develop AWS Lambda functions to start and stop

* * security-sensitive businesses, adoption of AWS best practice - A,C - Use AWS Config, Use AWS CloudTrail events to assess

* * 400TB, videos, 30 percent free capacity - D - Request multiple AWS Snowball devices to

* 172.31.0.0/24 VPC A, 172.50.0.0/16 VPC B - C - Create a VPC peering

* Redshift, become an integral part - B - template using AWS CloudFormation

* * AMIs, has an agile cicd, cannot be stalled - A,D - Use IAM policies, Use AWS Config rules to spot any, automatically terminate

* * never have public S3 objects - B,D - Configure an Amazon CloudWatch Events rule, Turn on object-level logging, detected in the AWS CloudTrail logs

* CloudFront, distribute both static and dynamic, ACM - D - two cache behaviors, Remove the User-Agent HTTP header from

* high-latency, call to 3-TB MySQL, VPN - D - RDS, monitor the AWS Lambda, Enable Auto Scaling in DynamoDB

* * video processing platform, uploaded by users - D - Rewrite the application, run from S3 and upload to S3

* patching plan - B - AWS Systems Manager

* design, REST service - API Gateway, Cognito

* * drastic increase in its monthly AWS spend, AD, Developers - D - SAML-based, PowerUserAccess, denies access except

* backup, at least two Avaliability Zones in each tier - D - Backup all in production, cross-region replication, in the second region to immediately move to Clacier

* Linux, NAS server - C - Expose an Amazon EFS share to on-premises, mount to EC2

* * CloudWatch Logs agent, aggregate all security events - C - Create Amazon Kinesis Data Streams in the logging account

* serverless, decrease time to deploy new version and revert - B - Use AWS SAM and built-in AWS CodeDeploy to

* .NET, XL storage, image and video files on local - B - Implement Auto Scaling, ELB, CloudFront, S3, Reserve, NO EFS

* * must deploy its application into other to meet low-latency - D - CloudFormation template, set from an administrator account, NO --region(s)

* * 30-TB repository of digital news videos, tape, MAM - A - AWS Storage Gateway, file gateway, No tape gateway

* B - Re-platform

* DNS may become overly complex - A - shared services VPC, peering, associate other VPCs with the hosted zone

* secure content management solution, API calls, rollback - A - S3, IAM role, SSE-KMS

* an audience in Aisa and South America - C - Lambda Edge

* "find a store near you" function, do not work about 50%, NAT - D - replacing, with a NAT gateway

* evaluate the configurations, collect metrics - A - deploy the data collection agent

* * SCPs, 1111-1111-1111 - C - Instruct the Developers to add Amazon S3 permissions to their IAM entities

* batch script is cumbersome to maintain - B - Kinesis agent, Auto Scaling group, Kinesis Data Forehose delivery stream, ES, Kibana

* storing the session information - D - ElastiCache with the Redis

* 8 m4.xlarge, Classic Load Balancer - C - Replace with m4.large in an Auto Scaling group

* data source feeds from local, will not be migrated, 10Gbit - D - Amazon ES, Elastic Beanstalk, ES cluster endpoint

* increasingly popular and experiencing latency, multi- region diaster - A - CloudFormation, Aurora, Route 53, cross-region database replica 

* performance can vary greatly depending on the time - B,D - Auto Scaling, CloudFront

* * catalogs, Amazon Mechanial Turk service - D - Use Amazon SWF to create a workflow

* * some orders were processed twice - C - EC2, Auto Scaling, Amazon SQS standard queues

* artifact, Apache HTTP server - B - VPC endpoint, aws:sourceVpce matching

* 2 PBs of genomic data - B - Have the organizations use their AWS credentials when

* * single 1Gbps, worried about the single point of failure - B - Set up VPN tunnels, VGW, BGP

* * pilot light approach for disaster recovery - D - Use EBS and RDS cross-region snapshot copy capability

* * small data records (up to 1KiB) for up to 30 days - A - Use Amazon S3 to collect mutiple records in

* tasked with automating the future deployments - B - Serverless Application Model, YAML, CodePipeline, CodeBuild, CLoudFormation

* all data, S3, must be encrypted - B,C - KMS-managed keys, CloudHSM client software, NO instances, NO IAM

* Java-based web service, at 30% CPU, by 10 times - A - Use AWS Elastic Beanstalk

* * limit access for terminating, to a small group of - B - Create a new tag-based IAM policy, apply this policy in each account

* * SLA of 99.95%, PostgreSQL - B - RDS Aurora PostgreSQL, Auto Scaling, EC2, AppStream 2.0, NO AWS Fargate containers

* 24 TB MySQL, 10 GB per day, go live, within 3 weeks - C - Create a database export locally, Snowball, RDS Aurora

* to a backend REST API hosted in the company - B - Install a second, from a different network carrier

* migrated to AWS as quickly as possible, 20TB - D - S3 Transfer Acceleration

* * Banking regulations, at least 7 years - C - feeds into an Amazon Kinesis Data Firehose

* HTTPS for encryption in transit, more secure - D - Add a VPC endpoint, from the VPC endpoint only

* DDoS, SQL injection - C - CloudFront, WAF, Shield Advanced

* * 100 AWS acccounts, one VPC per account - A - Create a transit VPC across two AZs, NO central VPC, NO VPC peering

* * e-commerce platform, LAMP stacks - B - Disable SSH access, RDS Multi-AZ, CloudFront, Shield, WAF, HAVE AWS Systems Manager to manage patching and allow the system administrators to run commands

* * (HPC) cluster, 5,000 cores and 10 petabytes of data - C - Store the raw data in Amazon S3, AWS Batch, Spot Fleets, EBS volumes

* 10PB of data, 1,000 AWS accounts, secure data sharing - D - a series of Amazon S3, AWS STS token

* containerized microservice-based, ECS - B,E - awsvpc network, IAM roles for tasks to

* VPC, EC2, RDS, runbook - D - CloudFormation, EC2 user data 

* * external vendor, only supports IPv6 - D - Create an egress-only internet gateway

* MySQL, heavy I/O, month-end load - B - Performing a one-time migration, RDS, serveral additional read replicas

* * storage layer for a data warehousing application - A - Store the data files in Amazon S3, use Range GET, NO EFS

* * EMR three m3.xlarge - B - master node in on-Demand, a mix of On-Demand and Spot, scheduled Reserved Instances

* * corporate credentials to access the AWS Console, AD - B - Create a two-way Forest trust relationship between

* * us-east-1, secondary VPC in us-west-2 - A - Provision a Direct Connect gateway, VGW, private VIF

* * web service, us-west-2 and us-east-1 - C,D - HTTP 2xx or 3xx, The specific text, 5,120 bytes

* * Ruby on Rails, MySQL, 16 TB - C - EC2, right-sized, Auto Scaling, Aurora, NO Lambda

* serverless video transcoding - A - Create roles with required service permissions, which are assumable

* commercial Apache Hadoop cluster - C - Develop a script, Athena, QuickSight

* ELB, RDS, must not be down for more than two hours - D - Configure a read replica in another region

* CloudFormation, difficult to deploy the services individually - C,E - CodePipeline, portfolio, Catalog, only to start the deployment

* * peak demand, Scripts and third-party deployment solutions - D - Docker, AWS Business Support, Trusted Advisor checks

* * Payment Card Industry (PCI) requirements - A - Build a multi-account strategy, SAML, OUs, NO PCI-compliant

* tight deadline, SQL Servers, import/export service - B - Enable Cost Explorer

* * replace its call system, deep learning - B,D,E - Amazon Connect, AWS Lambda, AWS Lex, NO SQS

* * Finance department, visibility into each group's spending - B,D - Use AWS Organizations, Enable all features of AWS Organizations and

* stream of 10 million, 100,000 sources each day - B - DynamoDB, TTL, after 30 days

* Oracle, 12 TB, BLOB - C - Use AWS DMS to load and replicate

* * grid environment, Grid instances - A,D,E - Enable VPC endpoints, Populate the on-premises DNS, Enable an interface VPC endpoint for EC2

* * patches, Systems Manager, reboots do not occur at the same time - C - AWSDefaultPatchBaseline, Define two non-overlapping

* * isolated, centralized administration - B - Create each instance, IAM roles, CLoudFormation StackSets, VPN gateway, NO VPC peering

* (HPC) cluster, 20 Gbps, The control instance - D - Move the control instance inside the placement group

* * invoking UpdateStack to replace the EC2 - C - attribute.UpdatePolicy

* master account and the secondary accounts - A,D - Send invitations to, SCP

* * AnyCompany has acuired numerours companies - A,D - Use consolidated billing to ensure, Create a federated identity, NO tagging solution

* * us-east-1 and eu-west-1, must be active in both - C,E - Use latency-based routing, Amazon RDS event notifications

* connect over RDP to troubleshoot issues - B - Run the host on AWS WorkSpaces

* * a large on-premises Apache Hadoop cluster, 20 PB HDFS - A - AWS Snowmobile, Reserved Instances, Spot Instances for task nodes

* .NET, Apache Cassandra - C - Elastic Beanstalk, Multi-AZ, DynamoDB

* * only allows specially hardened AMIs, terminate - D - CloudWatch Events, NO allowed AMIs

* migrate, 70 TB of static files, FASTEST - C - Re-platform, Snowball

* * application that generates a weather forecast - A - Amazon ES cluster, Enable API caching on the API Gateway stage

* * CloudFormation, concerned, RDS, EBS also be deleted - A - Modify the CloudFormation templates to add a DeletionPolicy attribute

* serverless, Lambda functions, hitting their timeout - B,D - Increase the amount of memory, Enable API cache

* deploy, fleet quickly - C - Use AWS Elastic Beanstalk, a blue/green deployment method

* web fleet to host a blog site, timeout, watch videos - C - CloudFront, from EFS to S3

* * runs its containerized batch jobs on Amazon ECS - C - ECS cluster, Fargate, NO Spot Instances

* A company receives clickstream data files - D - Lambda, S3 event notifications

* * new to AWS reports it has exhausted its service limits - D - CloudWatch, Lambda, Trusted Advisor, SNS, Business Support plan

* license that must be run on the same physical host - B - dedicated host with Host Affinity set

* * runs an IoT platform, Node.js API servers - C,E - Amazon Kinesis Data Streams, Lambda, Amazon DynamoDB, NO Aurora

* * store data from 2,000 internet connected sensors - A - Kinesis Data Streams, Lambda, S3, NO Firehouse

* bid processor is too slow during peak - C - Amazon SQS FIFO, a maximum size of 1

* A bank is re-architecting, (PCI DSS)-compliant - D - API Gateway, Lambda, SQS

* * migrating a 10 TB PostgreSQL, 50 MB with a VPN - D - Snowball, Schema Conversion Tool

* * Elastic Beanstalk using a blue/green deployment, next to complete - B - Select the Swap Encironment URLs option

* gain actionable insights using application logs - D - Install the Amazon CloudWatch agent on servers

* What combination of steps, from DDoS - B,D - Route53 + AWS Shield, CloudFront + WAF

* 10,000 to 150,000 images daily, Rekognition - A - Trigger AWS Lambda, DynamoDB, Amazon ES

* redesigning an image-viewing, SaaS, VDI - C - Elastic Beanstalk, CloudFormation, blue/green deployments

* Lambda functions, hitting their timeout - B,D - Increase the amount of memory, Enable API cache

* The encryption key must be managed by the company and rotated - B - S3, HTTPS, server-side encryption, AWS KMS

* any EC2 instance, recovers automatically, same IP - D - CloudWatch alarm, StatusCheckFailed

* * product catalog page, database tier, high load - B,E - a simple HTML page, ElastiCache cluster

* * uses Ohio (us-east-2), Virginia (us-east-1), backlog - B,C - SQS, multiple instances on the primary, NO additional elastic network interface

* deny access to a specific set of AWS services - B - to a single organization unit (OU), and apply the policy to that OU

* Oracle, will be changed to MySQL - B - AWS SCT, NO recommendation for the optimal database engine

* * to a running stack by first updating, then invoking UpdateStack - C - Edit the AutoScalingGroup resource, inserting an attribute.UpdatePolicy

* logging service, an SQS standard queue, order of the logs are not maintained - B - Delete the existing standard queue and recreate it as a FIFO queue

* analyze the trade data, for predictions for next day trading - A,C,D - S3, AWS Batch, EC2, auto-scaling

* * real-estate, renting, Java backend, MongoDB, tracing - A,C,D - autoscaling group of Java, DynamoDB, AWS X-Ray SDK

* * Lambda, show the statistics, 8:00AM GMT - A - CloudWatch Events rule, 00 08 * * * , NO Amazon Batch

* transformed the data, to another S3 bucket, Redshift, Glue - A,D - contains a crawler, has a central metadata repository(data catalog)

* 

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*

*
