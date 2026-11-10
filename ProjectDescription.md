# AWS WordPress Deployment & Monitoring

## 1. Project Overview

This project demonstrates the deployment, automation, and monitoring of a WordPress application on Amazon Web Services (AWS) using **AWS CloudFormation (Infrastructure as Code)**.

The objective was to establish a reliable and cost-optimised WordPress hosting solution for an organisation that publishes blogs and technical documentation.

To separate live workloads from testing activities, two independent environments were deployed:

- **Production Environment:** A continuously running WordPress instance to support live blog operations.
- **Development/Testing Environment:** An isolated WordPress instance automatically started and stopped during business hours (Monday–Friday, 9:00 AM–6:00 PM) using Amazon EventBridge.

The solution also incorporated Amazon CloudWatch for infrastructure monitoring and automated notifications to support operational reliability.

## 2. Project Objectives

- Deploy WordPress instances using AWS CloudFormation.
- Automate infrastructure provisioning through Infrastructure as Code (IaC).
- Establish separate Production and Development/Testing environments.
- Configure automated start and stop schedules for the Dev/Test instance.
- Monitor EC2 instance performance using Amazon CloudWatch.
- Configure CPU utilisation alarms and SNS notifications.
- Verify EC2 instance health using system and instance status checks.
- Reduce unnecessary compute costs by restricting Dev/Test operating hours.

## 3. AWS Services and Technologies Used

| AWS Service | Purpose |
|---|---|
| AWS CloudFormation | Automates infrastructure provisioning using templates. |
| Amazon EC2 | Hosts the WordPress application. |
| Amazon VPC | Provides an isolated virtual network environment. |
| Public Subnet | Enables public access to the WordPress web server. |
| Internet Gateway | Provides internet connectivity to the public subnet. |
| Security Groups | Controls inbound and outbound network traffic. |
| Amazon EventBridge | Automates scheduled starting and stopping of Dev/Test instances. |
| Amazon CloudWatch | Monitors EC2 performance and infrastructure health. |
| Amazon SNS | Sends automated monitoring notifications. |
| Apache | Provides web server functionality. |
| PHP | Supports WordPress application execution. |
| MariaDB | Provides database functionality for WordPress. |

## 4. Solution Architecture

The infrastructure was designed using AWS CloudFormation to provide repeatable and consistent deployments.

### Architecture Components

The solution consists of the following components:

1. **Client Request:** Users access the WordPress application through a web browser.
2. **Public Subnet:** Hosts the EC2 instance and enables public application access.
3. **Security Group:** Controls inbound traffic, allowing HTTP (Port 80) and SSH (Port 22) access as configured.
4. **Amazon EC2:** Runs the WordPress application and its supporting services.
5. **Apache Web Server:** Handles incoming HTTP requests.
6. **PHP Engine:** Executes WordPress application code.
7. **MariaDB:** Stores WordPress application data.
8. **Amazon CloudWatch:** Monitors instance performance and generates alarms.
9. **Amazon EventBridge:** Manages scheduled start and stop operations for the Dev/Test environment.

### WordPress Application Stack

| Component | Configuration |
|---|---|
| Compute | Amazon EC2 |
| Instance Type | t3.micro |
| Operating Environment | Linux |
| Web Server | Apache |
| Application Runtime | PHP 7.2 |
| Database | MariaDB |
| CMS | WordPress |
| Network Access | HTTP (80), SSH (22) |

## 5. Implementation and Deployment

### Phase 1: Infrastructure as Code – AWS CloudFormation

A self-contained AWS CloudFormation template was developed to provision the required infrastructure.

Since the lab environment did not provide a default VPC, the template included the necessary networking components.

**Resources Provisioned:**

- Virtual Private Cloud (VPC).
- Public Subnet.
- Internet Gateway.
- Route Table and Routes.
- Security Group.
- EC2 Instance.
- WordPress application hosting components.

**Implementation Highlights:**

- Defined infrastructure resources using CloudFormation templates.
- Automated network and compute resource deployment.
- Configured security group rules for application access and administration.
- Used reusable templates to maintain consistency across environments.

**Outcome:** Established an automated and repeatable infrastructure deployment process without manually creating each AWS resource.

### Phase 2: Production and Development Environment Deployment

Two independent CloudFormation stacks were deployed to separate live workloads from development and testing activities.

| Environment | CloudFormation Stack | Purpose | Operating Schedule |
|---|---|---|---|
| Production | `wordpress-production` | Hosts the live WordPress application. | 24/7 |
| Development/Testing | `wordpress-development` | Provides an isolated environment for testing and development. | Monday–Friday, 9 AM–6 PM |

**Deployment Steps:**

1. Uploaded the CloudFormation template to AWS CloudFormation.
2. Created the Production stack named `wordpress-production`.
3. Created the Development stack named `wordpress-development`.
4. Applied the resource tag `Environment=Dev` to identify the development environment.
5. Verified successful stack creation and EC2 instance deployment.
6. Tested WordPress application accessibility.

**Outcome:** Successfully established separate production and development environments, reducing the risk of development activities affecting the live application.

### Phase 3: Dev/Test Automation Using Amazon EventBridge

Amazon EventBridge was configured to automate the operating schedule of the Development/Testing EC2 instance.

The objective was to ensure that development resources operated only during business hours, reducing unnecessary compute consumption.

**1. Start Schedule – 9:00 AM**

| Parameter | Configuration |
|---|---|
| Rule Name | `Dev-Start-9AM` |
| Cron Expression | `cron(0 9 ? * MON-FRI *)` |
| Target Service | Amazon EC2 |
| Target API | `StartInstances` |
| Target Resource | WordPress Dev/Test EC2 Instance |

**2. Stop Schedule – 6:00 PM**

| Parameter | Configuration |
|---|---|
| Rule Name | `Dev-Stop-6PM` |
| Cron Expression | `cron(0 18 ? * MON-FRI *)` |
| Target Service | Amazon EC2 |
| Target API | `StopInstances` |
| Target Resource | WordPress Dev/Test EC2 Instance |

**Automation Workflow:**

1. Created scheduled EventBridge rules.
2. Configured the start rule to execute at 9:00 AM, Monday to Friday.
3. Configured the stop rule to execute at 6:00 PM, Monday to Friday.
4. Selected the relevant EC2 instance as the target.
5. Configured the required IAM execution permissions.
6. Verified the scheduled instance start and stop operations.

**Outcome:** Automated Dev/Test infrastructure management, reducing unnecessary EC2 running hours and supporting more efficient resource utilisation.

### Phase 4: Monitoring and Observability Using Amazon CloudWatch

Amazon CloudWatch was configured to monitor EC2 instance performance and identify potential infrastructure issues.

**CloudWatch Alarm Configuration:**

| Parameter | Configuration |
|---|---|
| Metric | `CPUUtilization` |
| Threshold | Greater than 80% |
| Evaluation Period | 1 |
| Period Duration | 5 minutes |
| Notification Service | Amazon SNS |
| Notification Target | Team Administrator |

**Implementation Steps:**

1. Navigated to Amazon CloudWatch.
2. Selected the EC2 CPU utilisation metric.
3. Created a CloudWatch alarm with a threshold greater than 80%.
4. Configured one evaluation period of five minutes.
5. Integrated the alarm with an Amazon SNS notification topic.
6. Configured the team administrator as the notification recipient.
7. Verified the alarm configuration.

**EC2 System Health Verification:**

EC2 status checks were used to verify instance health.

- System Status Check: Verified.
- Instance Status Check: Verified.
- Overall Health: 2/2 status checks passed.

**Outcome:** Established infrastructure monitoring and automated notifications to support early detection of performance-related issues and instance health problems.

## 6. Project Outcomes and Key Achievements

The project successfully delivered an automated WordPress hosting solution using AWS infrastructure services.

Key achievements include:

- **Infrastructure Automation:** Used AWS CloudFormation to provision networking and compute resources.
- **Environment Separation:** Deployed independent Production and Dev/Test environments.
- **Scheduled Automation:** Implemented EventBridge schedules to start and stop development resources during business hours.
- **Cost Optimisation:** Reduced unnecessary Dev/Test compute running hours through automated scheduling.
- **Performance Monitoring:** Configured CloudWatch CPU utilisation monitoring and threshold-based alarms.
- **Automated Notifications:** Integrated CloudWatch with SNS for monitoring alerts.
- **System Health Monitoring:** Verified EC2 instance health through status checks.
- **Operational Reliability:** Established a structured approach to application deployment, monitoring, and resource management.

## 7. Key Skills Demonstrated

- AWS CloudFormation and Infrastructure as Code (IaC)
- Amazon EC2 Instance Deployment and Management
- AWS VPC and Network Configuration
- Security Groups and Network Access Control
- WordPress Application Deployment
- Apache, PHP and MariaDB Configuration
- Amazon EventBridge Scheduling and Automation
- EC2 Start/Stop Automation
- Amazon CloudWatch Metrics and Alarms
- Amazon SNS Notifications
- Infrastructure Monitoring and Troubleshooting
- Cloud Resource Optimisation
- Production and Development Environment Management

## 8. Conclusion

This project provided practical experience in designing, deploying, and managing a WordPress hosting solution using AWS cloud services.

By combining CloudFormation, EC2, EventBridge, CloudWatch, and SNS, the solution demonstrated how Infrastructure as Code, scheduled automation, and proactive monitoring can improve infrastructure management.

The separation of production and development environments helped protect live workloads, while automated scheduling reduced unnecessary compute usage in the development environment.

---

**Technology Stack:** AWS | CloudFormation | EC2 | VPC | EventBridge | CloudWatch | SNS | WordPress | Apache | PHP | MariaDB
