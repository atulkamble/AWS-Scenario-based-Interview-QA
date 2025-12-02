# 🚀 **AWS Scenario-Based Questions with COMPLETE Detailed Answers (1–80)**

*(Real-world, hands-on, architect-level)*

---

# 🟦 **1. EC2 & Linux Administration Scenarios**

---

## **1. EC2 instance becomes unreachable — how to troubleshoot?**

### **Answer: Follow the 5-layer troubleshooting model**

#### **Layer 1 — Network (VPC)**

* Check **Security Group inbound rules** → allow port `22` (SSH) or `3389` (RDP).
* Check **NACLs** → ensure inbound/outbound are not DENY.
* Check **Route Table**:

  * Public subnet must route `0.0.0.0/0` → Internet Gateway.
* Validate **Public IP** exists if using SSH over internet.

#### **Layer 2 — Instance Level**

* Check **EC2 system status checks**:

  * *Instance reachability check failed*: OS issue.
  * *System reachability failed*: AWS infra issue → open support ticket.

#### **Layer 3 — Key Pair / Authentication**

* Wrong key pair
* Wrong user (`ec2-user`, `ubuntu`, `centos`)
* Permissions on PEM file not `400`

#### **Layer 4 — OS-level issues**

* Disk full
* Broken SSHD
* Bad firewall rules

### **Recovery Method**

Use **EC2 Rescue / SSM** or
**Detach EBS → attach to rescue instance → fix files → attach back**.

---

## **2. Host a static website quickly — which instance & config?**

### **Best Approach**

Do **NOT** use EC2. Use **S3 Static Website Hosting + CloudFront**.
Cheaper, fully managed, global delivery.

### **If EC2 must be used**

* Use **t2.micro / t3.micro**
* Install nginx/httpd
* Use **Auto Scaling + ALB** if expected traffic
* Attach **S3-based CI/CD** or CodeDeploy for updates.

---

## **3. EC2 root EBS volume is full — how to increase?**

### **Steps**

1. Stop instance (optional for gp3).
2. Go to EC2 → Volumes → Modify Volume
3. Increase size (e.g., 8 → 50GB)
4. Log into EC2:

   ```
   sudo growpart /dev/xvda 1
   sudo resize2fs /dev/xvda1
   ```
5. Validate with `df -h`.

---

## **4. Lost EC2 private key — how to regain access?**

### **3 recovery options**

#### **Option A — Create image → launch new instance**

1. Stop instance
2. Create AMI
3. Launch new instance with new key pair

#### **Option B — Detach root volume method**

1. Stop instance
2. Detach root volume
3. Attach to another instance
4. Modify `~/.ssh/authorized_keys`
5. Reattach

#### **Option C — Use SSM Session Manager**

If SSM agent + IAM role exists → no need for SSH.

---

## **5. SSH into EC2 without PEM key — solutions?**

### **Approaches**

1. **SSM Session Manager** (BEST, secure, no port 22)
2. **EC2 Instance Connect**
3. **IAM-based temporary SSH keys**

---

## **6. EC2 CPU spikes daily — how to auto-scale?**

### **Design**

* Use **Auto Scaling Group (ASG)**
* Define CPU alarms:

  * Scale Out: CPU > 70% for 5 mins
  * Scale In: CPU < 30% for 10 mins
* Use **Launch Template**
* Place behind **Application Load Balancer**
* Use **Warm pools** for faster scale-out

---

## **7. Zero-downtime deployment on EC2 — architecture?**

### **Use Case: Web App on EC2**

Use **Blue-Green Deployment** using:

* **ALB + Target Groups**
* **CodeDeploy (Blue/Green mode)**

### **Flow**

1. Blue = current version
2. Green = new version
3. Deploy to Green
4. Test with traffic mirroring or test URLs
5. Switch ALB target group
6. Rollback if needed

---

## **8. Avoid placing API keys inside EC2 code — how?**

### **Correct Approaches**

* **IAM Roles for EC2** (best)
* **AWS Systems Manager Parameter Store** (SecureString)
* **AWS Secrets Manager**
* Server-side encryption with KMS

---

## **9. Migrate EC2 between regions — steps?**

1. Stop instance
2. Create AMI
3. Copy AMI to another region
4. Launch instance in new region
5. Update:

   * Security Groups
   * Elastic IPs
   * DNS (Route53)
6. Validate application
7. Final cut-over

---

# 🟩 **2. VPC & NETWORKING (10–17)**

---

## **10. Private subnet EC2 cannot access internet — why?**

### **Cause**

Private subnet → no IGW access

### **Fix**

* Add **NAT Gateway** in public subnet
* Update private subnet routing to:

  ```
  0.0.0.0/0 → NAT Gateway
  ```

---

## **11. SSH into private EC2 — secure?**

### **Best options**

1. **SSM Session Manager**
2. **Bastion Host (Jump Server)**
3. **EC2 Instance Connect Endpoint (New)** → SSH to private subnet without IGW

---

## **12. Choose VPC Peering vs Transit Gateway**

### **VPC Peering**

* Small number of VPCs
* Full mesh required
* No transitive routing

### **Transit Gateway**

* 5+ VPCs
* Hub-spoke
* Transitive routing
* Multi-account communication

---

## **13. Lambda in VPC cannot access external API**

### **Needs:**

* NAT Gateway
  OR
* VPC Endpoint (if AWS service)

### **Checklist**

* Lambda ENI created?
* Subnet has route to NAT?
* SG allows outbound traffic?

---

## **14. NAT Gateway cost high — alternatives**

* **NAT Instance**
* **Split VPC** (place some services public)
* **Use VPC Endpoint for S3, DynamoDB**
* **Use ECR VPC endpoints**

---

## **15. Multi-region VPC design**

* Use identical CIDR blocks or non-overlapping? → Prefer non-overlapping
* Create **Transit Gateway peering**
* Use **Route53 Failover**
* Cross-region VPC Peering for low-latency apps

---

## **16. On-Prem to AWS — choose VPN vs DX vs TGW**

* **VPN**: quick, cheap, 1 Gbps
* **Direct Connect**: stable, dedicated 1/10/100 Gbps
* **TGW**: large enterprise, multiple VPN + DX combined

---

## **17. Restrict VPC communication using SG vs NACL**

### **Security Groups**

* Instance level
* Stateful
* Easiest

### **NACL**

* Subnet level
* Stateless
* Deny rules possible
  Use for **team segregation**.

---

# 🟨 **3. S3 & STORAGE (18–24)**

---

## **18. S3 Website "Access Denied"**

### **Reasons**

* Bucket Policy missing
* Block Public Access ON
* Using wrong region in URL

### **Fix**

* Disable **Block Public Access**
* Add policy:

  ```json
  {
    "Effect": "Allow",
    "Principal": "*",
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::yourbucket/*"
  }
  ```

---

## **19. S3 bill increasing — how to reduce cost?**

* Use **S3 Storage Lens**
* Enable **Lifecycle Policies**:

  * Standard → IA → Glacier → Deep Archive
* Compress objects
* Delete incomplete multipart uploads
* Use **Intelligent Tiering**

---

## **20. Enforce encryption on all S3 objects**

Add bucket policy:

```json
{
  "Effect": "Deny",
  "Principal": "*",
  "Action": "s3:PutObject",
  "Resource": "arn:aws:s3:::bucket/*",
  "Condition": {
    "StringNotEquals": {"s3:x-amz-server-side-encryption": "AES256"}
  }
}
```

---

## **21. Cross-Region Replication**

### Requirements:

* Versioning enabled
* IAM role
* Destination bucket policy
* Replication config

---

## **22. Uploading 5GB+ files — architecture**

Use **Multipart Upload**:

* Parallel chunk uploads
* Resume support
* Faster

Use **S3 Pre-Signed URLs** for direct browser → S3 uploads.

---

## **23. Secure S3 for customer KYC**

* S3 Private bucket
* KMS SSE-KMS
* Object Lock
* VPC Endpoint
* CloudTrail for audit
* Least privilege IAM

---

## **24. S3 + CloudFront + Route53 global static website**

* Upload website to S3
* CloudFront distribution
* Route53 → Alias to CloudFront
* Enable caching, edge optimization
* Use OAC (Origin Access Control)

---

# 🟥 **4. IAM & SECURITY (25–30)**

---

## **25. IAM user cannot access S3 bucket even with full access**

### **Check**

* Block Public Access
* SCP (Service Control Policy) at Org level
* Bucket policy Deny
* IAM permissions boundary
* Explicit Deny rules

---

## **26. Enforce MFA for all users**

* IAM → Account Settings → Require MFA
* Use IAM Policy:

  ```json
  "Condition": {"BoolIfExists": {"aws:MultiFactorAuthPresent": false}}
  ```

---

## **27. Temporary AWS Access — best choice**

* **STS AssumeRole**
* **IAM Role with 1 hour session duration**
* **SSO-based access**

---

## **28. Audit all IAM policy changes**

Use:

* **CloudTrail**
* **Config Rule: iam-policy-change**
* **SNS Alerts**

---

## **29. Least-privilege IAM for microservices**

* One IAM role per microservice
* Permission boundaries
* Use attribute-based access control (ABAC)
* Fine-grained S3 access (prefix-level)

---

## **30. Automate key rotation every 30 days**

Use:

* Lambda (cron event)
* IAM ListAccessKeys → Delete old → Create new
* Notify through SNS

---

# 🟪 **5. DATABASE (31–36)**

---

## **31. RDS slow — checks**

* CPU
* Memory
* IOPS
* Slow query logs
* Free storage
* Parameter group misconfigurations

---

## **32. Enable automated backups**

* Modify RDS
* Set Backup retention → 7–35 days
* Choose backup window

---

## **33. Cross-region DR for PostgreSQL**

* **Read Replica (Cross Region)**
* Promote during disaster
* Route53 failover

---

## **34. DynamoDB read cost too high**

Fix:

* Use **DAX** caching
* Use **Query instead of Scan**
* Reduce item size
* Use **LSI/GSIs** efficiently

---

## **35. Near-zero downtime RDS migration**

Options:

* DMS (continuous replication)
* Blue/Green deployment
* Read replica promotion
* Snapshot restore + cutover

---

## **36. DynamoDB Global Tables vs Replication**

### **Global Tables**

* Multi-master
* Active-active
* Conflict resolution

### **Custom replication**

* SQS/SNS/Lambda
  Use when business logic required.

---

# 🟧 **6. LOAD BALANCERS & HA (37–42)**

---

## **37. ELB health checks failing**

Check:

* App responds at `/health`
* SG inbound allows LB
* Wrong port
* Deregistration delay too low
* App startup slow

---

## **38. Balance traffic across 3 EC2**

Use:

* **Application Load Balancer**
* Target group with 3 EC2
* Health checks enabled

---

## **39. Sticky sessions**

Enable:

* ALB → Target Group → Stickiness
* TTL (1–7 days)

---

## **40. Auto Scaling launching too many instances**

Review:

* Cooldown
* Incorrect CloudWatch alarms
* Memory-based scaling not supported → use custom metrics
* Health checks failing causing replacement loops

---

## **41. Multi-AZ HA Architecture**

* ALB
* Auto Scaling across 2 AZs
* RDS Multi-AZ
* S3
* CloudFront
* Route53

---

## **42. Multi-region active-active**

* Global Accelerator
* Route53 latency routing
* DynamoDB Global Tables
* S3 CRR
* Active-active load balancing

---

# 🟫 **7. SERVERLESS (43–48)**

---

## **43. Lambda timeout issues**

Fix:

* Increase timeout
* Optimize code
* Place Lambda inside VPC if DB access
* Check network latency
* Provisioned Concurrency

---

## **44. Lambda cannot access DB in VPC**

Check:

* Subnet routing
* SG rules
* NAT for public access
* ENI attached to right subnet

---

## **45. API Gateway 5XX**

Causes:

* Lambda timed out
* Wrong proxy integration
* Missing IAM role
* Bad mapping template

---

## **46. Secure Serverless API with JWT**

Use:

* API Gateway Authorizer
* Cognito User Pool
* Lambda claims verification

---

## **47. Event-driven architecture**

Flow:

* S3 → Event → SQS → Lambda → DynamoDB
* Durable, scalable, serverless

---

## **48. Reduce Lambda cold start**

* Use **Provisioned Concurrency**
* Keep dependencies small
* Use ARM64
* Avoid VPC unless needed

---

# 🔵 **8. DEVOPS / EKS / ECS (49–55)**

---

## **49. Docker image too large**

Fix:

* Use **multi-stage builds**
* Use smaller base image (alpine)
* Remove cache, logs
* `.dockerignore`

---

## **50. CodePipeline deploy fails**

Check:

* IAM role
* Artifact bucket
* CodeDeploy AppSpec
* Buildspec errors

---

## **51. ECR image pull failing**

Need:

* `ecr:GetAuthorizationToken`
* `ecr:BatchGetImage`
* `ecr:GetDownloadUrlForLayer`

---

## **52. ECS stuck in Pending**

Causes:

* No free EC2 capacity
* Port conflict
* Wrong subnets
* No public IP
* IAM missing

---

## **53. EKS ImagePullBackOff**

Check:

* Wrong image URI
* No ECR permissions
* Private registry secret missing
* Node cannot pull (NAT required)

---

## **54. CI/CD Pipeline for microservices**

* GitHub → CodeBuild → ECR → Helm → EKS
* Canary deployments
* Use Service Mesh (AppMesh/Istio)

---

## **55. EKS Autoscaling**

Includes:

* HPA (CPU/Memory metrics)
* Cluster Autoscaler
* Managed Node Groups
* Metric Server

---

# 🔴 **9. MONITORING (56–61)**

---

## **56. CloudWatch agent not pushing logs**

Check:

* IAM role
* `amazon-cloudwatch-agent.json`
* Logs directory permissions
* Agent running

---

## **57. CPU alarm never triggers**

Reasons:

* Wrong metric namespace
* Wrong period
* Missing data treated as "ignore"

---

## **58. Log-based metric filter**

Steps:

1. Go to Logs
2. Create Pattern
3. Create Metric
4. Alarm from metric

---

## **59. Real-time alerting setup**

Use:

* CloudWatch → Metric Filter
* SNS notifications
* Lambda for custom alerts

---

## **60. Centralized logging**

Architecture:

* CloudWatch Logs → Kinesis Firehose → S3/OpenSearch
* Kibana dashboards

---

## **61. EKS monitoring dashboard**

Use:

* CloudWatch Container Insights
* Grafana (managed)
* Prometheus operator
* Node exporter

---

# 🟣 **10. COST OPTIMIZATION (62–67)**

---

## **62. Monthly bill increased suddenly**

Check:

* Cost Explorer
* Top services
* EC2 idle
* NAT data processing
* S3 PUT requests
* Data Transfer

---

## **63. Reduce EC2 costs**

Use:

* Spot Instances
* Savings Plans
* Rightsizing
* Auto Scheduling (stop non-critical)

---

## **64. Reduce NAT Gateway cost**

* Use VPC Endpoints
* NAT instance
* Consolidate subnets
* Cache outputs

---

## **65. Reduce RDS cost**

* Use Serverless
* Stop non-production after hours
* Downsize instance
* Storage auto-scaling
* Increase caching

---

## **66. Cost-optimized SaaS architecture**

* EKS with spot
* Aurora serverless
* S3 for static
* CloudFront
* KEDA autoscaling

---

## **67. Reserved Instances vs Savings Plans**

### **RIs**

* Per instance family
* Up to 72% discount

### **Savings Plans**

* Apply to ANY compute
* Flexible
* No instance lock-in

---

# 🟤 **11. MULTI-REGION (68–73)**

---

## **68. RDS restore**

Steps:

* Snapshots → Restore to new instance
* Point-in-time recovery

---

## **69. S3 cross-region restore**

* Enable replication
* Use S3 Inventory and restore operations
* Glacier restore if archived

---

## **70. On-Prem → AWS migration**

Tools:

* **SMS** → Server migrations
* **DMS** → DB migration
* **Snowball** → Large data

---

## **71. Multi-AZ vs Multi-Region**

* Multi-AZ → HA
* Multi-Region → DR + Global availability

---

## **72. Full DR strategy**

Includes:

* RPO/RTO definition
* Cross-region replication
* Route53 failover
* Automated failback
* Database replicas

---

## **73. Multi-region active-passive**

* Primary region active
* Secondary warm standby
* Route53 failover
* Replication for DB + S3

---

# 🟠 **12. BIG INTERVIEW REAL SCENARIOS (74–80)**

---

## **74. Production outage at 2AM — strategy**

1. Check CloudWatch alarms
2. ELB health
3. ASG scaling events
4. DB connections
5. Logs
6. Rollback latest deployment
7. Document RCA

---

## **75. Multi-tenant SaaS**

* Cognito per-tenant
* VPC-per-tenant / shared VPC
* IAM isolation
* DynamoDB partitioning
* S3 prefix policy

---

## **76. Handle 10M daily users — architecture**

* CloudFront
* S3 for assets
* Microservices on EKS
* DAX + DynamoDB
* Aurora Serverless
* Global load balancing

---

## **77. Secure CI/CD pipelines**

* OIDC access
* KMS
* Code signing
* Secrets Manager
* IAM least privilege
* Audit trails

---

## **78. PCI/HIPAA compliant system**

* Encryption in transit + rest
* HSM
* VPC isolation
* Config rules
* GuardDuty
* Audit logging
* WAF

---

## **79. Observability stack**

* CloudWatch Metrics
* Logs
* X-Ray traces
* Managed Grafana
* Alerts + dashboards

---

## **80. Blue-Green / Canary deployment**

* CodeDeploy (EC2/ECS)
* App Mesh / Istio for canary
* Weighted target groups
* Route53 gradual rollout

---
