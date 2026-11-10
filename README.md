WordPress Deployment & Monitoring

1. Executive Summary -
This project delivers a resilient, scalable, and cost-optimized WordPress hosting architecture on Amazon Web Services (AWS) using Infrastructure as Code (IaC). To support the organization’s publishing requirements while protecting production workloads, two isolated environments were provisioned:
1.	Production : A continuous, high-availability WordPress instance.
2.	Development/Testing (Dev/Test): An isolated sandbox environment automated via Amazon EventBridge to run exclusively during business hours (9:00 AM – 6:00 PM), reducing compute overhead by over 70%.

Core Components -
•	AWS CloudFormation: Automated single-click provisioning of network infrastructure (VPC, Subnet, Route Tables, Internet Gateway) and compute resources.
•	Amazon EC2 (t3.micro): Hosting environment executing Apache, PHP, MariaDB, and WordPress core components.
•	Amazon EventBridge Scheduler: Business-hours scheduler executing target management API calls (StartInstances / StopInstances).
•	Amazon CloudWatch: Real-time system performance monitoring and metric alerting.

4. Deployment Steps & Technical Implementation -
Phase 1: Infrastructure as Code (CloudFormation Template)
To overcome the lack of default VPC availability in the lab environment, a comprehensive, self-contained CloudFormation template was authored to deploy the network layer alongside the application stack.

Phase 2: Deploying Production and Dev Stacks
1.	Executed Stack 1: wordpress-production to host persistent production blog operations.
2.	Executed Stack 2: wordpress-development with the key-value tag Environment: Dev applied to isolate testing operations.
Phase 3: Dev/Test Automation Schedule (9 AM – 6 PM)
To restrict the Dev environment's operation to standard business hours, Amazon EventBridge rules were created targeting the instance ID of WordPress-Dev-Stack:
•	Start Rule (Dev-Start-9AM):
o	Cron Expression: cron(0 9 ? * MON-FRI *)
o	Target API: EC2:StartInstances
•	Stop Rule (Dev-Stop-6PM):
o	Cron Expression: cron(0 18 ? * MON-FRI *)
o	Target API: EC2:StopInstances
Phase 4: Observability & Monitoring Setup
1.	CloudWatch Metrics Alarm:
o	Metric: CPUUtilization
o	Threshold: $> 80\%$ for 1 period of 5 minutes.
o	Action: Direct Simple Notification Service (SNS) notification to team administrator.
2.	System Health Verification:
o	Continuous tracking via native EC2 status checks (2/2 checks passed).

