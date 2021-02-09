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

* design, REST service - C - API Gateway, Cognito

* * drastic increase in its monthly AWS spend, AD, Developers - D - SAML-based, PowerUserAccess, denies access except

* backup, at least two Avaliability Zones in each tier - D - Backup all in production, cross-region replication, in the second region to immediately move to Clacier

* Linux, NAS server - C - Expose an Amazon EFS share to on-premises, mount to EC2

* * CloudWatch Logs agent, aggregate all security events - C - Create Amazon Kinesis Data Streams in the logging account

* serverless, decrease time to deploy new version and revert - B - Use AWS SAM and built-in AWS CodeDeploy to

* .NET, XL storage, image and video files on local - B - Implement Auto Scaling, ELB, CloudFront, S3, Reserve, NO EFS

* * must deploy its application into other to meet low-latency - D - CloudFormation template, set from an administrator account, NO --region(s), NO --region

* * 30-TB repository of digital news videos, tape, MAM - A - AWS Storage Gateway, file gateway, No tape gateway

* B - Re-platform

* DNS may become overly complex - A - shared services VPC, peering, associate other VPCs with the hosted zone

* secure content management solution, API calls, rollback - A - S3, IAM role, SSE-KMS

* 50 - an audience in Aisa and South America - C - Lambda Edge

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

* * long~, DynamoDB, 70%, Lambda, ProvisionedThroughtputExceededException - A - NO Increase the number of shards in the Kinesis data stream

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

* * call center, task up to 72 hours to be transcribed, NFS - A - S3, Lambda + Amazon Transcribe, NO Mechanical Turk

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

* * cannot be directly connected to the internet, sandboxes exceed $500 - B,C,F - Organization, prevent use of the default VPC, SCP, AWS Budgets + report on monthly sending, NO AWS Config

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

* * CloudFront cache hit ratio, sometimes in lowercases letters - A - Lambda@Edge, force them to lowercase

* RDP over the internet to access - D - Site-to-Site VPN

* migrate, AWS Application Discovery Service, Oracle, PostgreSQL - B,D - Migrate, Oracle + SCT, PostgreSQL + DMS

* * Keep page load times to within 50 ms, serverless - A - Simple AD, NO AppSync, NO Amazon Cognito

* 70% increase in sales, private GitHub, Jenkins, rolled back - B - GitHub webhooks, Jenkins plugin, blue/green

* REST API, Player session data, low-latency - C - API Gateway, Lambda, DynamoDB

* EC2, S3, never traverse the public internet - C - VPC endpoint, IAM role, bucket's policy

* * Production Cutover, Migrate - A - SMS, initial testing, NO CLI, NO Application Discovery Service

* sell tickets, Oracle, performance issues - C - On-Demand and Spot Instances, Convert, DynamoDB tables

* 403, S3, AWS KMS key - B - permission to decrypt the referenced KMS key

* static web, S3, CloudFront, 403 - D - Create a new IAM role, new role as its OAI

* database password, rotated every 60 days - B - Secrets Manager, NO UserData, NO Ref intrinsic

* is slow from time to time, resolve the us-east-1 performance issues - B,D - Create a new S3 bucket in us-east-1 + synchronize, Use Lambda@Edge + in us-east-1, NO S3 Transfer Acceleration

* * CloudFront, secured during end-to-end transit and at rest - B,D,E - Acquire a public certificate from a third-party vendor, using AWS KMS, Use SSL or encrypt + VPN, NO AWS Certificate Manager, NO explicit encryption

* * Amazon SageMaker, Jupyter - C - Sagemaker:NotebookInstance resource type, mapping them to Small, Large and X-Large

* * least priviledge access, central account, enforce tags - B,D,E - Service Catalog to portfolios, have ServiceCatalogEndUserAccess permissions + copy the TagOption, Use AWS Service Catalog TagOption Library

* S3 buckets with versioning enabled, CI/CD - A - Use AWS CodeBUild, EventBridge, AWS CDK

* RDS for MySQL, SSD, show 40% to 50% CPU and memory, root cause - A - It exhausted the I/O credit

* * Java based, CloudFront, users reported errors and timeouts - A,B,D - Aurora + publish slow query and error logs + CloudWatch Logs, AWS X-Ray SDK, CloudWatch Logs agent, NO CloudTrail, NO performance benchmarking

* * an active-passive configuration with the us-west-1 region - B - ALB + Route53, a failover routing policy

* errors occur, there is a call to DynamoDB - B - DynamoDB auto scaling on the table

* * EMR, budgetary threshold - A,D - CloudFormation + AWS::Budgets..Budget + NotificationsWithSubscribers, AWS Service Catalog portfolio

* a gene reporting device, near-real-time analytics - B - Amazon Kinesis Data Streams, Redshift cluster using Amazon EMR

* * WordPress site in a colocated data center - A - one AWS Region and three, a price class that includes the US and Europe, NO EFS Cross Region replication

* SAML 2.0, through the federated identity web portal, not able to access the AWS - B,D,F - The IAM roles created for the federated, STS AssumeRoleWithSAML, Idp defines SAML assertions, NO DNS

* * DX and IPSec VPN, cannot traverse the internet, offering its services to other companies - A - VPC Endpoint Service, accepts TCP traffic, NO internet gateway, NO NAT gateway

* * Active Directory, two domain controllers, not able to resolve - A,B - Define an outbound Amazon Route 53 Resolver, forward all non-authoritative queries to the VPC Resolver

* * A fitness tracking company, read-heavy - C,E - Route53 + failover routing, Set up active-active web

* NoSQL, us-west-2, us-west-1 disaster recovery, Failing back - A - Use DynamoDB global tables

* serverless, easy rollback mechanism - C - A canary deployment

* AWS PrivateLink, client services, unable to submit logs - A,C - NACL + NLB, security group + allows ingress from the NLB subnets

* laptops, in transit must be encrypted between users and the VPC - D - Create a new private virtual interface, over the DX private virtual interface

* * consists of both static, S3, dynamic, ECS, CloudFront - A,C,F - WAF + web ACL + ALB, Configure CloudFront to add a custom header to origin requests, OAI

* * S3, a signed URL to allow, anyone can download - B - Review the AWS Trusted Advisor bucket permissions check and implement the recommended actions, NO Block Public Access

* a lot of communication between business units with very large data transfers - B - Create a transit gateway

* popular video game, public download - C - Route53, S3, CloudFront

* RDS, DynamoDB, AWS Billing, no consistent tagging strategy - C - Use Tag Editor, SCPs

* adjust the deployment process to support a canary release - A - Create an alias for every new, AWS CLI update-alias command

* RTO of 30 minutes, RPO of 5 minutes, 50TB, Aurora - B,D - Deploy a hot standby, Create a cross Region Aurora

* org1, org2, least privilege - D - ARN, external ID

* * 90 days, 200 virtual machines, 40 TB - B,C - AWS SMS to migrate, Use AWS Storage Gateway, NO Snowball

* AWS Fargate, cannotPullcontainerError, 111122223333.dkr.us-east-1 - B - DISABLED for the auto-assign public IP, NAT gateway in the public subnet

* * 50 TB Oracle, Redshift, 30 days to complete - B - AWS Snowball import job, RDS for Oracle, NO AWS SCT

* * security-approved AMIs, be scanned every 30 days - C,D - Use Amazon Inspector + CVE, Lambda + AWS Config rule

* * migrate, NAS, message queue - D - SQS, EC2, Auto Scaling group, S3

* local market to a national market, MySQL - A,B,E - EC2, Application Load Balancer, Aurora, static website content + S3 + CloudFront

* * ticketing system, planned maintenance - D,E - Lambda + SQS, Amazon EventBridge + SQS

* forecasting, database will store approximately 10 TB - B - RDS, read replica in a secondary Region

* retrieves credentials, from an encrypted file in Amazon S3 - A - Secrets Manager, Screts Manager RotationSchedule

* users in Europ are reporting slow performance for their image uploads - C - S3 Transfer Acceleration

* uses access keys, on each instance to access - A,C,F - instance profiles, Systems Manager Parameter Store, Systems Manager Session Manager

* promotion, a sign-up page - B - Use Amazon SQS to decouple

* NAS, 40 TB, migrate - B,C - SMS, Storage Gateway

* * disaster recovery, CMK - B,C,E - s3GetObjectVersionForReplication, S3 RTC, symmetric AWS KMS CMK

* provide a consistent user experience - C - Aurora Replicas, round robin routing, sticky sessions enabled

* * replicate, S3, not impact other critical Lambda functions - A - Set the new Lambda Function reserved concurrency limit, NO S3 event notifications

* video game, public download - C - Route 53, S3, CloudFront

* * developer, read-only access to all Amazon S3 - C,D - SCPs, permissions boundaries set for the IAM user, NO bucket policies

* * migrate, SAN, Java and PHP, MySQL, Oracle - A,D,F - Application Discovery Service, CART, Migration Hub, NO Inspector, NO SMS, NO X-Ray

* video game, gone viral, load time, has increased - B - S3, CloudFront, Lambda@Edge

* * 20 instances, 10.0.0.0/23, AZ1, 10.0.0.0/24, 10.0.1.0/24 - A - Delete and re-create the AZ1

* * HPC, 12-node cluster, on a single rack - B,C - in a placement group, support Elastic Fabric Adapter, NO bustable performance

* * Java, Tomcat, CloudFormation, Chef, frequent service disruptions - A - blue/green, NOT canary

* end-to-end view, analyze the latency - A - AWS IAM, execute-api:Invoke, AWS X-Ray

* CloudFormation template have caused unplanned downtime - B - CodeBuild, test environment, blue/green

* * Site-to-Site VPN, Aurora, NAT gateways - B - Detach + NAT gateways, Aurora Serverless database, NO Reserved Instance, NO stop and start

* HPC, 1,000 EC2 instances, maximum performance - A,C,F - single Availability Zone, EFA, FSx for Lustre

* automate, container image - A,C,F - Configure an Amazon ECR, Configure an AWS CodeBUild, initiates an AWS CodeBuild build.

* near-real-time, all AWS CloudTail logs and VPC Flow logs, ES - B - Amazon Kinesis Data Firehouse

* * are in TypeScript and Python, eliminate, such as looping - D - Define the AWS resources using TypeScript or Python, AWS CDK, NO re-use parts of

* need autonomy to modify route tables, Centralized, egress - D - A shared transit gateway

* migrate 50 TB of NFS, Snowball Edge, very slow, overhead of encrypting - B - Change the solution to use the S3 Adapter

* * blog post application, comments appears in real time - C - Use AWS AppSync, NO cache API responses

* Each, AWS IoT, sends a message every 30 seconds, database is not updating - D - MQTT

* * staging and production workloads, calculate the AWS costs for each project - A,D,F - taggable resources, AWS Service Catalog, SCPs + AWS Organizations, NO Cost Explorer

* * game player-matching service, high level os security - A,D,F - NLB, nework ACL + block all non-UDP, AWS Shield Advanced, NO ALB

* Able to fail over in 1 minute - B,C,E - Route 53 + TTL to 30 seconds, global table within Amazon DynamoDB, Use Spot Instances for the required resources, NO Reserved Instances

* * low latency and random access to 100 GB of data, 3000 IOPS - A - EFS + Max I/O, NO 1-TB EBS + gp2

* VMware, Physical servers, Red Hat Enterprise Linux - B - Application Discovery Service Discovery Agent on each of

* design a cross-Region data recovery solution - B - Aurora global database with the primary, secondary

* should be blocked from purchasing Reserved Instances - B - Create a new organizational unit (OU), Deny effect

* Metadata, ElastiCache for Redis cluster - c - AWS Step Functions workflow, Lambda, SQS, 

* has multiple lines of business, a single AWS invoice, restrict services - B,D - invite each LOB's AWS account, Create an SCP that allows only

* * 1000 AWS accounts, 540 developer, be moved to the new developer organization - B,E,F - From the master account, Call the InviteAccountToOrganization, Have each developer sign in

* centralized solution so each business unit receives monthly reports - B - AWS Budgets in the organization's master account, SNS topic, Use Cost Explorer in the organization's master account

* multi-account, must have connectivity with one another - C - two DX partners, transite gateway, each DX interface

* * bastion host, only TCP port 22, 0.0.0.0/0, failed SSH logins - A - AWS Systems Manager, Session Manager, NO Systems Manager Run Command

* Organizations, restrict, certain AWS Regions, must be tagged - D - with a new OU, tag policy, SCP

* * cost reports, specific projects, resource tagging - A,D - Create an AWS Cost and Usage Report rule, Create an AWS Budgets report, Attach the budget for each

* * nine member accounts, consolidation of AWS CloudTrail logs - C - organization-level CloudTrail, Migrate the existing, to the central S3, Delete the existing, in the member accounts

* * six partners, is originating from a botnet, wants to secure - D - AWS WAF, Create a usage plan, NO CloudFront, NO POST

* * Timesheets, submitted, on Friday - A,E - EC2 On-Demand + on Fridays, S3 + Athena + QuickSight, NO CloudFront, NO Redshift

* * five physical data centers, 10 Gbps, VPN, migrate - C - CloudEndure Migration agent onto each physical machine, Create a migration blueprint

* single server, migrate - B - Aurora, ElastiCache for Redis

* hospitals, sensor data, keep aanalysis tools running locally - C - Aurora Serverless database, Aurora Data API

* * hybrid solution, events in near-real time, semi-structured JSON - A,D - Use Amazon Kinesis Data Firehose to buffer, Amazon ES

* VMs, collect data for the initial migration - C - Application Discovery Service agent, Hyper-V, Agentless Discovery Connector, Athena, QuickSight

* * EBS snapshots, at least two additional AWS Regions - A - Amazon DLM

* * citizens to submit and retrieve sensitive documents - B,D - AWS WAF web ACL + rate-based rule, Kinesis Data Firehose delivery stream + Amazon ES, NO CloudFront

* SNS, thrid-party alerting system - C Organizations master account, service-managed permissions, automatic deployment

* migrate the database, without any data loss or downtime - C - Aurora DB Cluster, DMS, a full load with continuous replication

* * bank, statements be retained for at least 7 years - C - with Object Lock enabled, Enable compliance mode with a default retention period of 2 years

* * stock exchanges, Kafka, near-real-time - A,C,D - Establish an AWS Direct Connect, Producer Library to put, Create a WebSocket API

* * development accounts, communicate with each other, production, isolate - C - Create separate route tables, NO tag

* upload and search random photos - C - S3 + SQS

* * deliver its video-on-demand (VOD) content - B - Use AWS Elemental MediaConvert, NO S3, NO Glacier Deep Archive

* * SAN, gemomics analysis, 200 GB of data for each genome - D - Use an AWS Storage Gateway, NO DataSync

* analyze faulty order messages - D - Configure a new SQS standard queue

* Low latency, End-toend encrytion, DDoS protection - A,C - Route 53 + CloudFront, AWS Shield Advanced

* * a fleet of Amazon EC2 Reserved Instances, 80% RI coverage - B - SNS, Use the Cost Exporer console

* a mix of Windows and Linux VMs, migrate, documentation, is not up to date - C - Install the AWS Application Discovery Service on each of the VMs

* * CloudFormation, encryption of all AMI snapshots, EBS - B - AWSServiceRoleForAutoScaling service-linked role

* * has 60 TB, a solution that does not require custom development - A - AWS DataSync agent, NO AWS Snowball device

* Docker, EC2 On-Demand, MongoDB, EC2 Reserved Instance - B,D - AWS Fargate, DocumentDB

* * CRM, user name and password, audit, rotated - A - Create a new AWS Systems Manager Parameter Store entry, NO AWS Secrets Manager

* connected to shared VPC, CIDR blocks overlap - A - AWS Transit Gateway

* electronic copies of their receipts, up to 1 week to download - B - S3, SES

* * s3-elb-logs, central account, consolidation - A,E - allow the PutBucketLogging action for the central, SSE-S3

* The thrid party accepts only one public CIDR - B - assign them to the NAT gateways

* cloud.example.com, Route 53 - D - Associate + to the shared services VPC, inbound resolver

* These files will be processed once and must be retained for 1 year - D - S3, cross-Region replication, DynamoDB + global table

* * a mix of java and Node.js - B - ECS, memory utilization of 75%

* * media metadata extraction pipeline, SNS, CPU utilization on the database is high - C,E - RDS proxy, SQS + standard queue

* increase security controls around SSH access - D - AmazonSSMManagedInstanceCore policy, Systems Manager Session Manager plugin

* SaaS, ingests several files daily, SFTP - A - AWS Transfer for SFTP endpoint

* Only users from a specific country are allowed, log the access, blocked - A - IPSet, WAF web ACL

* * A car rental company, spradic database memory errors - D - Enable throttling, Set the rate and burst values to limit the incoming calls

* * desktop as a service, WorkSpaces, access files, on premises, SSO - A - Create an AWS Directory Service, ADMT

* * a large-scale migration to AWS, central cloud team, monitor, exceeds 80% utilization - B - EventBridge, Trusted Advisor service limites checks, SNS, NO AWS Config

* * restaurant review, fake posting every day, restrict the bots - B - AWS WAF web ACL, managed IP reputation, NO Firewall Manager

* public-facing, ALB - A - IPSet

* receives metrics from IoT devices in JSON format, JDBC - C - Re-architect, S3, Glue, Glue Data Catalog, Athena

* * CMK, must be rotated once every 365 days - B - Create a customer managed CMK, Enable automatic rotation

* * to three different third-party, be encrypted, no more than 500 Mbps - A - Use AWS Certificate Manager (ACM), HTTPS TLS

* * mechanism for retaining failed orders, decoupled - C - S3, AWS AppSync for database API services, SQS dead-letter queue for retaining failed orders, NO Elastic Beanstalk

* * remove sensitive information, S3, KMS - A,D - Create an AWS KMS + AWS Logs Delivery, Create a new CodeCommit + Create a new CodePipeline pipeline

* * using an existing on-premises Active Directory - B - Deploy an AWS Control Tower landing zone, Create an AD Connector linked to, NO SAML, NO SCPs

* CloudFormation, Allow, Update:* , accidentally removing or replacing, RDS - C - Add a second, Deny, Update:Delete, Update:Replace

* * Windows file server, be available on a file system in the cloud - B - AWS DataSync, Amazon FSx, NO EFS

* * disaster recovery (DR), RPO of 5 minutes - C - Use Amazon EC2 Image Builder, NO every 5 minutes, NO another running copy

* * digital wallet application, Support1, finance1 - B - From the master account, assume the OrganizationAccountAccessRole, NO Support1, NO finance1
6
