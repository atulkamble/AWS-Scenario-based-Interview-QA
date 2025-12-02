# ✅ **AWS Scenario-Based Questions (Comprehensive List)**

Organized by **Service / Domain** and includes **Beginner → Intermediate → Advanced** levels.

---

# 🟦 **1. EC2 & Linux Administration Scenarios**

### **Beginner**

1. Your EC2 instance becomes unreachable. What steps do you take to troubleshoot?
2. You need to host a static website quickly — which instance type and configuration will you choose?
3. Your EC2 instance root EBS volume is full. How will you increase storage?

### **Intermediate**

4. Your EC2 instance accidentally lost its private key. How do you regain access?
5. A developer wants to SSH into EC2 without sharing a PEM key — what AWS services solve this?
6. EC2 instance CPU spikes at 90% daily — how do you auto-scale?

### **Advanced**

7. You need zero-downtime deployment for an app running on multiple EC2 instances — which architecture will you design?
8. Your EC2 app needs secret/API key access — how do you avoid storing secrets inside code?
9. You must migrate an EC2 instance from one region to another — explain the end-to-end steps.

---

# 🟩 **2. VPC & Networking Scenarios**

### **Beginner**

10. Your application on private subnet EC2 cannot access the internet. Why and how to fix it?
11. You need SSH access to EC2 in private subnet — design a secure approach.

### **Intermediate**

12. Two VPCs need to communicate — how do you choose between VPC peering vs Transit Gateway?
13. Your Lambda in private subnet needs to hit an external API — what VPC components do you need?
14. NAT Gateway costs are high — what are cheaper alternatives?

### **Advanced**

15. Design a multi-region VPC architecture ensuring cross-region communication.
16. Your on-premises data center must connect to AWS. How do you choose between VPN, Direct Connect & TGW?
17. You need to restrict VPC communication between teams — how do you use AWS Network ACLs vs SGs?

---

# 🟨 **3. S3 & Storage Scenarios**

### **Beginner**

18. Your S3 website suddenly shows "Access Denied." How do you fix bucket policies?
19. S3 cost increasing — how do you analyze and reduce storage cost?

### **Intermediate**

20. You need to enforce encryption on all S3 objects — how do you apply bucket policy?
21. Create cross-region replication between two buckets with minimal downtime.

### **Advanced**

22. Your user uploads large 5GB files — explain a performant architecture for uploads.
23. Design a secure S3 architecture for storing customer KYC documents.
24. S3 + CloudFront + Route53 → Design a global low-latency static website.

---

# 🟥 **4. IAM & Security Scenarios**

### **Beginner**

25. A user cannot access an S3 bucket even with full-access policy. What troubleshooting steps?
26. You must enforce MFA for all users — how will you do that?

### **Intermediate**

27. A developer needs temporary AWS access — which IAM approach do you use?
28. You need to audit all IAM policy changes — which AWS services help?

### **Advanced**

29. How do you design least-privilege IAM roles for a microservices architecture?
30. You must rotate keys every 30 days — automate the solution.

---

# 🟪 **5. RDS / DynamoDB / Databases Scenarios**

### **Beginner**

31. Your RDS instance is slow — what initial checks do you perform?
32. How do you enable automated backups in RDS?

### **Intermediate**

33. Your RDS PostgreSQL needs cross-region DR — what AWS services do you use?
34. DynamoDB read costs are too high — what optimizations do you apply?

### **Advanced**

35. Your production RDS needs near-zero downtime migration — what strategies?
36. DynamoDB global tables vs replication architecture — when to choose what?

---

# 🟧 **6. Load Balancers, Auto Scaling & High Availability**

### **Beginner**

37. ELB health checks failing — how do you troubleshoot?
38. You need to balance traffic across 3 EC2 instances — which ALB/ELB approach?

### **Intermediate**

39. Your application needs sticky sessions — what LB configuration helps?
40. Auto Scaling is launching too many instances — how to optimize policies?

### **Advanced**

41. Design a multi-AZ Highly Available architecture for a web app.
42. Design a multi-region active-active architecture for global users.

---

# 🟫 **7. Lambda, API Gateway, Serverless Scenarios**

### **Beginner**

43. Lambda function timeout increasing — what checks do you perform?
44. Lambda cannot access database in VPC — how to fix?

### **Intermediate**

45. API Gateway returning 5XX — how do you troubleshoot?
46. Build a secure serverless API with JWT authentication.

### **Advanced**

47. Design an event-driven architecture using SQS + Lambda + DynamoDB.
48. Lambda cold-start issues — how do you reduce them?

---

# 🔵 **8. DevOps, CI/CD, Docker, ECR, EKS, ECS**

### **Beginner**

49. Your Docker image size is too large — what optimizations?
50. CodePipeline fails to deploy — what troubleshooting steps?

### **Intermediate**

51. Your ECR image pull from EC2/EKS is failing — what IAM permissions required?
52. ECS service is stuck in "Pending" state — root causes?

### **Advanced**

53. Kubernetes pods failing with `ImagePullBackOff` — full debugging steps.
54. Design a CI/CD pipeline for microservices using CodePipeline + CodeBuild + EKS.
55. EKS cluster needs autoscaling — design full architecture (NodeGroup + HPA + Cluster Autoscaler).

---

# 🔴 **9. CloudWatch, Monitoring, Logging**

### **Beginner**

56. CloudWatch agent is not pushing logs from EC2 — what checks?
57. CPU alarm never triggers — why?

### **Intermediate**

58. How to create a log-based metric filter in CloudWatch?
59. Your application logs need real-time alerting — design the setup.

### **Advanced**

60. Design centralized logging using CloudWatch + Kinesis + OpenSearch.
61. Build a monitoring dashboard for EKS workloads.

---

# 🟣 **10. Cost Optimization Scenarios**

### **Beginner**

62. Monthly bill increased suddenly — how will you identify cost drivers?
63. EC2 on-demand cost is high — what alternatives?

### **Intermediate**

64. NAT Gateway cost too high — redesign low-cost architecture.
65. RDS idle cost high — what strategies reduce cost?

### **Advanced**

66. Design a cost-optimized architecture for a startup SaaS platform.
67. Explain Reserved Instances vs Savings Plans — when to choose what?

---

# 🟤 **11. Multi-Region, DR, Backup & Migration**

### **Beginner**

68. RDS backup restore — how?
69. S3 cross-region restore process.

### **Intermediate**

70. On-prem to AWS migration — what tools (SMS, DMS, Snowball) and when?
71. Multi-AZ vs Multi-Region HA — compare.

### **Advanced**

72. Design a full DR strategy (RPO/RTO, Route53 failover, DB failover).
73. Multi-region active-passive architecture for an enterprise application.

---

# 🟠 **12. Real Interview Scenarios (High Difficulty)**

74. Your production system goes down at 2AM — walk through your troubleshooting strategy.
75. You need to design a secure multi-tenant SaaS on AWS — how?
76. An app handling 10M users daily needs scalability — design architecture.
77. Explain how you will secure CI/CD pipelines end-to-end.
78. Build an audit-ready, compliant system following **PCI / HIPAA** on AWS.
79. Design an observability stack using CloudWatch + Grafana + X-Ray.
80. How do you implement blue-green or canary deployments with zero downtime?
