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

