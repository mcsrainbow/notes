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

* - B - Re-platform

* DNS may become overly complex - A - shared services VPC, peering, associate other VPCs with the hosted zone

* secure content management solution, API calls, rollback - A - S3, IAM role, SSE-KMS

* an audience in Aisa and South America - C - Lambda Edge

* "find a store near you" function, do not work about 50%, NAT - D - replacing, with a NAT gateway

* (52) 

* 
