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

* (82)

*

*
