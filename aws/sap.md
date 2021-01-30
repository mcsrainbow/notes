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

* gp2, modifying the type - A,D - 50GB gp2 root, io1, 1TB gp2, st1

* * "HLS" method of viewing the Kinesis video stream - A,D - A web application, Playback video by typing in the HLS

* * recognize faces, MKV, AWS Rekognition - B,C,F - A Kinesis video stream + utMedia, An Amazon Rekognition Video stream processor, A Kinesis data stream consumer

* have turned off the CloudTrail, prevent - A - SCP, deny policy, cloudtrail:StopLogging

* * AWS Congnito, DynamoDB, integrate with Google - C - Cognito User pools, Google access in Cognito User pool

* * holiday shopping traffic, Hadoop cluster with Cascading - D - Put the source data to a Kinesis stream, EMR

* SQL Server, Oracle, snapshots - D - AWS Data Lifecycle, tags

* * Keen.io, BadRequestException, InternalErrorException - B,D - 400 and 500 + Method Response, Add Integration Responses

* * discount restaurant, VPC endpoint, S3 - A,D - Use a VPC Endpoint policy, S3 bucket policy that denies all actions if, NO security group

* Amazon Managed Blockchain - A - an initial member, or a network invitaion can be created for

* production and testing, better isolation - A - Create a new AWS account to hold user

* * CISO, wants one central place - A - Deine AWS IAM roles, SAML-based, NO SCP

* Cloud Center of Excellence team - D,E - Set up AWS Organizations, Using a master AWS account, assume the roles

* * To abide by industry regulations, headquarters - D - two AWS Direct Connect, Use Direct Connect Gateway

* * a mix of Java and Node.js, single deployment, - B - Deploy Amazon ECS, 75%, NO Elastic Beanstalk

* * long~, must build a highly available infrastructure for a popular gobal video game - C,D - Set value of Evaluate Target Health, Write a URL, NO weight of 100

* online e-commerce, 100 GB memory requirement, 250 miles - C - Use a scaled-down version

* memory-intensive - D - using R5 instances, CLoudWatch agent, Standard Reserved Instances, NO Spot

* * datacenter, 500 Mbps AWS Direct Connect, 1 Gbps ISP - D - S3 Transfer Acceleration, NO 80 TB AWS Snowball

* serverless, providers such as Google, baseline DDoS protections - C - CloudFront, S3, Cognito, API Gateway, Lambda

* * AWS CodeCommit, AWS CodePipeline - A - Use AWS CodeBuild for, CodeBUild to stage

* * ordering system, SQS, Lambda, JSON - A - multiple messages, and error handling, a dead letter queue, NO long polling

* same IP CIDR range, 10.0.0.77 - D - create a static route, 10.0.0.77/32, VPC peer 

* REST API hosted, be routed over an AWS Direct Connect - B - Install a second Direct Connect, from a different network carrier

* * stores invoice files in Amazon S3, DynamoDB - D - DynamoDB global table, continuous backup, S3 cross-region replication

* online shopping website, man-in-the-middle - A - Route 53, Enable DNSSEC, ACM, TLS/SSL, NO root servers, NO 2048-bit

* * least priviledged access, billing - D - Create a master account for billing using Organizations

* * 24 TB MySQL, 10 GB per day, 50 Mbps VPN, within 3 weeks - C - database export locally, using AWS Snowball, NO DMS

* perform SQL queries, market segments, Team Manager - B - SCP that denies, Amazon Athena, S3, CloudTrail

* long~, single high-memory, in-memory queue - B - API Gateway, Amazon SQS

* two Linux Servers behind, license, tied to the MAC address - A,D - Create a pool of ENIs, Create bootstrap automation to attach an ENI from the pool

* re-engineer, current CI/CD, patch deployments - B - Code Deploy configured for blue/green deployments

* * DNS record must be removed after, Throttling, Rate exceeded - A,C,D - FIFO queue, CloudWatch Events rule, Successful, Lambda + SQS, NO standard queue, NO Kinesis

* containerized NET Core, AWS Fargate, SQL Server - B - Multi-AZ + SQL Server + RDS, ECS, NO non-persistent empty storage

* * refactoring an existing web service - D - Store the data in Amazon Aurora global databases

* * OS patches, 48 hours - A - Use AWS Manager to create a new AMI, AWS CodeDeploy, NO batch job

* * switch to Amazon CloudFront, CMS, 404 - A,D - Add another origin to the CloudFront, Add a behavior to the CLoudFront

* * Approximately 2% of the call recording - A - with Amazon Transcribe

* * More OUS and AWS accounts will continue to be created - C - Apply this SCP at each OU level

* tens of thousands of simultaneous - C - The throttle limit set on API Gateway is very low

* * A retail company, NET web, user's session locally - A,C,E - ElastiCache, RDS for SQL Server, instances in an Auto Scaling group, NO CloudFront

* point-of-state data, speed greater than 2 Gbps - A - Establish a second DX connection, DynamoDB Global tables

* logs personality identifiable informationj, HSMs - C - Create a CMK, with no key material, EXTERNAL, from on-premises HSMs 

* 10.0.0.0/24, 10.0.1.0/24, NACL - B,E - inbound + source 10.0.0.0/24, outbound + 1024 + 65535 + 10.0.0.0/24

* internal AWS Elastic Beanstalk, external payment gateway API - A - NAT gateway

* users to upload videos, restricted content - B - Keep + T2, Reserved + On-Demand, Spot Fleet, EC2 C4 + C5 Spot

* * The Quality Assurance (QA) - B - AWS Service Catalog product, NO CLoudFormation

* each team has their own Amazon RDS, totals 100 TB - D - Glue crawler, use Amazon Athena to run the queries

* * provides AWS solutions to its users with AWS CLoudFormation templates - D - Cloud Development Kit constructs, NO S3

* * Long-lived credentials should not be used - B - be assumed by Amazon EC2 only, allows the newly created IAM role to use

* * A fleet of Amazon ECS, SQS queue is filling up - D - The ECS task role was modified

* * long~, DynamoDB, 70%, Lambda, ProvisionedThroughtputExceededException - D - Increase the number of shards in the Kinesis data stream

* * only authenticated users are allowed to post content, larger than 100 MB - C - Enable an S3 Transfer acceleration endpoint, presigned URL, NO authorizer

* CISO, reg-engineer, CI/CD - B - blue/green

* using date ranges, 10 GB of data every day, Athena - C,E - Parquet, dt=2019-02

* * advisory firm, EMR, isolated from the internet, encrypted - A,E - KMS CMK, aws:sourceVpce, NO CloudHSM

* * IoT, stale data, avoid causing disruptions - A - use strongly consistent reads, from their home AWS Region

* * private law firms, cannot be stored outside the country - C - Tag documents as either regular or secret, Lambda, NO CloudWatch Events

* 100 virtual machines, 40 TB, migration, within the next 3 months - D - AWS Snowball

* Provisioned ThroughtputExceeded exceptions - B,D - Enable DynamoDB auto scaling, Implement the DynamoDB Accelerator (DAX)

* * legacy infrastructure provisioning scripts to AWS CloudFormation templates - C - AWS Code Pipeline, create and execute

* * track AWS costs in Organizations - C - Use Cost Explorer, NO Lambda

* * call center, task up to 72 hours to be transcribed, NFS - A - S3, Amazon Transcribe, NO Mechanical Turk

* rapidly scale from hundreds to tens of thousands of jobs in less thant 30 seconds - B - Lambda, SQS

* * the first set of applications that run on Linux - A - a VPN, AWS Systems Manager Session Manager, NO AWS Direct Connect

* stay connected, even when there are underlying failures - B - EC2, Auto Scaling group, ALB, DynamoDB

* * Each record is less than 4 KB, store the data for 120 days only - B - DynamoDB, Time to Live (TTL)

* Procurement Managers, Private Marketplace, role - C - AWSPrivateMarketplaceAdminFullAccess, an organization root-level SCP, NO developers

* shipements in the company's warehouse - C - DynamoDB, AWS Step Functions workflow, in progress, scanned and fulfilled

* ecommerce, S3, DynamoDB, separate Region - A,B,D - Route 53 health checks, S3 cross-Region, DynamoDB global tables

* * new business analytics, reguires 10,000 hours - C - ECS, Fargate, Spot Fleet

* transformed, CloudFormation - C - AWS Code Pipeline, create and execute

* * is written in NET, MySQL, 200,000 daily users - B - CloudFormation, ALB, EC2 Aurora, Route 53

* * Developers must launch the AWS infrastructure using AWS CloudFormation - A,E - Using CloudFormation + can be assumed by CloudFormation, In a central AS account + can be assumed by CloudFormation

* putting IAM user secret access keys in their code - D - Code Commit trigger, Lambda, disable, notify

* Each time a user logs in, the stored procedures run, DB2 CPU licensing - B - AWS DMS, DynamoDB

* A fleet of Amazon ECS, SQS, 400 errors, no throttling - D - The ECS task role was modified

* * need a 500 GB static dataset, system(os) patches, within 48 hours - A - a new AMI, Code Deploy to push, NO batch job every night

* modify existing Reserved Instances, submit requests to a dedicated team - A,D - Organizations, SCP

* * cannot be directly connected to the internet, sandboxes exceed $500 - B,C,F - Organization, prevent use of the default VPC, SCP, report on monthly sending, NO cloudfront

* * Developers to use only Amazon EC2, S3 and DynamoDB, SCP - B - Remove the FullAWSAccess SCP from the Developer account's OU

* time-of-use metering, many ProvisionedThroughputExceededException errors - A,D - Increase the write capacity units to the DynamoDB table, Stream the data into an Amazon Kinesis data stream

* DNS name my.service.com, fixed address assignments - C - one Elastic IP address for each, NLB, Assign the Elastic IP addresses to the NLB for each

* * m5.2xlarge, db r4.4xlarge, RDS, confirmed to be optimal - B - Reserve capacity for the rds database and the minimum number of EC2

* Prevent ingress from port 22, billing, tags, Encrypt all Amazon EBS - B - Use AWS Service Catalog to build a portfolio

* a username and password, can be viewed, Lambda function code - A,B - Lambda to assume, Secrets Manager

* HTTP webhook, code vulnerability scanning, Lambda, only one, for each - B - Amazon SQS FIFO queue

* * a breach of highly confidential personal information, S3 - B - Athena, Systems Manager Automation

* * AWS WAF, prevent SQL injection attacks, breach was reported - D - Amazon GuardDuty, VPC Flow Logs, EventBridge, Lambda, NO CloudFront

* a proof of concept, Lambda functions were hitting their timeout - B,E - Increase the amount of memory, Enable API cache

* CloudHSM, authorize, quorum - D - Create an Amazon Cognito, POST method

* * CloudFront, Lambda@Edge, users in Australia - C - Configure S3 transfer Acceleration, Edge endpoints for Australia and Europe

* * pipelines across North America and, imagery data - D - AWS IoT Greengrass, Ship the devices back to

* * DNS, Account A, Account B, db.example.com CNAME, new VPC - C,E - Create an authorization to associate, Associate a new VPC in Account B with

* * CloudFront, Error 403 - A,B - Remove the S3 block public access option, Remove the requester pays option

* all internal, use private IP addresses - B - Enable the private DNS option

* * S3 uploads have been failing, storage data costs - A - multipart upload, AWS CLI

* orchestration tool, penetration test, AWS Config - D - Systems Manager Agent, current orchestration tool, Migrate, RDS, Systems Manager Patch Manager, Systems Manager Maintenance Windows task

* to be available at all times, Orders - C - SQS, FIFO, Reserved Instances

* re-architect the application to reduce operational overhead - C - Host the web application in Amazon S3

* * supports 300,000 web requests each minute, CDN - B - in a single AWS Region, minimum desired capacity, 450,000 requests, support 100% of the peak read queries

* * Rejected requests must be sent to a third-party auditing - D - Muti-AZ Auto Scaling group, ALB, Amazon Kinesis Data Firehose, AWS Managed Rules in AWS Marketplace

* * and read-only access to resources for all other projects - D - Create a customer managed policy document, specify full control, project-specific policy, NO project tag

* CI/CD pipeline, able to roll back - B - AWS Elastic Beanstalk, swap, environment URLS.

* API error, 111122223333.dkr.ecr - B - Configure a NAT gateway in the public subnet

* photo sharing, image processing, before publishing, Python - B,D - Lambda, CloudFront

* acquired a new business, existing AWS Config rules - D - Onboarding, move the organization's root SCP to the Production OU

* * SQL Server, AWS Schema Conversion Tool, 1 TB, grows less than 1 MB - B - Create an AWS DMS replication instance and task, NO snapshot, NO native backup

* * PAN data, automatically mask, to JSON - A - Lambda, SQS, NO Fargate, NO Glue, NO EMR

* Chef tools, roll back in 5 minutes - D - Use AWS OpsWorkds, blue/green, NO canary deployment

* * custom-built solution for authenticating - A,E - Use a custom-built SAML, AWS SSO + Amazon Cognito, Use a custom-built OpenID, NO LDAP

* * An online magazine, almost all traffic is read-only - D,E - Aurora global database, physical cross-region, Route 53 with latency-base

* * ap-southeast-2, ALB, ACM, TLS, cannot cause application downtime - C,D,F - Create a new ACM certificate in the us-east-1, Update Route 53, Update the ALB + CloudFront Edge locations only

* usage has gone from a few hundred to millions of users - B - S3, Intelligent Tiering storage class, CloudFront

* serverless, ECS, firt 3 months, and rarely after that - ECS + a fleet of Spot, RDS + Reserved, S3 Glacier

* (263)

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
