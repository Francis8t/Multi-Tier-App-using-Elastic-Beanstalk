# Deploying a 3-Tier Web Application on AWS using AWS Elastic Beanstalk

For a more comprehensive, step-by-step guide with accompanying screenshots, please refer to the full article on my [Medium page](<https://medium.com/@francis8t/step-by-step-guide-to-deploying-a-3-tier-web-application-on-aws-with-cloudfront-distribution-d97c031fb517>).

## Overview
Automating deployment tasks with AWS Elastic Beanstalk has helped many companies significantly reduce deployment and operational costs. This guide walks through deploying a 3-tier web application on AWS using Elastic Beanstalk, covering essential AWS services and configurations.

## AWS Services Used
- **Elastic Beanstalk**: Automates infrastructure setup for an Apache Tomcat app, including ELB and ASG.
- **S3**: Stores static content for the web app.
- **RDS**: Hosts the MySQL database.
- **ElasticCache**: Caching service for frequently accessed data.
- **ActiveMQ**: Message queue service between the application and data tiers.
- **Route53**: DNS service for the AWS environment.
- **CloudFront**: CDN for global content distribution.

> **Note**: AWS services incur charges. Ensure to terminate them when finished.

## Architectural Layout
1. **Presentation Tier (Frontend)**: Amazon Route 53, CloudFront, and S3.
2. **Application Tier (Business Logic)**: ELB, Elastic Beanstalk with ASG, and Amazon MQ.
3. **Data Tier (Database and Caching)**: ElasticCache (Memcached) and Amazon RDS.

## Execution Flow
1. Log in to AWS Account.
2. Create a key pair for Elastic Beanstalk instance access.
3. Set up a security group for backend services (ElasticCache, RDS, ActiveMQ).
4. Create RDS, ElasticCache, and ActiveMQ instances.
5. Launch an EC2 instance for database initialization.
6. Set up the Elastic Beanstalk environment.
7. Configure Beanstalk health-check and add HTTPS listener to ELB.
8. Build and deploy application artifact on Beanstalk.
9. Update ELB endpoint in GoDaddy DNS with SSL certification.
10. Set up CloudFront distribution.

## Detailed Step-by-Step Guide

### Step 1: Log in to AWS Account
Log into AWS.

### Step 2: Create Key Pair for Beanstalk Instance
   - **Steps**: `Services → EC2 → Network & Security → Key Pairs`.
   - Choose `RSA` key type and `.pem` format for Linux compatibility.

### Step 3: Set Up Security Group for Backend Services
   - **Steps**: `Services → EC2 → Network & Security → Security Group`.
   - Allow internal traffic among ElasticCache, RDS, and ActiveMQ.

### Step 4: Create Instances
   - **RDS**: Create subnet and parameter groups, configure MySQL, and secure access.
   - **ElasticCache**: Configure Memcached with custom parameter and security groups.
   - **ActiveMQ**: Deploy in private access mode with RabbitMQ instance.

### Step 5: Launch EC2 for Database Initialization
   - Launch a client EC2 instance in the same VPC as the MySQL database.
   - Install MySQL client, clone the repository, and import the `db_backup.sql` file.

### Step 6: Set Up Elastic Beanstalk Environment
   - **Navigate to Elastic Beanstalk**: Go to `Services → Elastic Beanstalk → Create application`.
   - **Platform**: Choose **Tomcat** as the platform type.
   - **Service Access**: Select an IAM role with Elastic Beanstalk Admin permissions.
   - **EC2 Key Pair**: Choose the key pair created earlier to enable SSH access.
   - **Public IP**: Enable public IP addresses for EC2 instances, if needed.
   - **Database**: Skip the database creation segment since an RDS instance has already been set up.
   - **Capacity Management**:
     - Define the minimum and maximum number of instances for the auto-scaling group.
     - Select a scaling metric (e.g., CPU utilization, NetworkOut).
   - **Load Balancer**: Choose **Application Load Balancer** to handle HTTPS traffic.
   - **Rolling Updates and Deployments**: Configure deployment methods, such as `rolling` or `all-at-once`.
   - **Environment Configuration**: Review all details and confirm the creation of the Elastic Beanstalk environment.

   Once the setup is complete, test the Elastic Beanstalk domain URL to ensure it leads to the expected page.

### Step 7: Configure Beanstalk Health-Check and HTTPS Listener
   - Set health-check path to `/login`.
   - Add HTTPS listener using an SSL certificate from AWS Certificate Manager.

### Step 8: Build and Deploy Application Artifact
   - Clone the repository and update `application.properties` with specific endpoints.
   - Run `mvn install` to create a deployable `.war` file.
   - Deploy the `.war` file in Elastic Beanstalk.

### Step 9: Update ELB Endpoint in GoDaddy DNS
   - Add a CNAME record to map Beanstalk URL to custom DNS.

### Step 10: Test Application
   - Verify login using `admin_vp` as username and password.

### Step 11: Set Up CloudFront Distribution
   - Create a CloudFront distribution with the ELB as the origin.
   - Map the CloudFront domain to the custom DNS.

## Conclusion
This project deploys a robust 3-tier web app on AWS, emphasizing scalability, security, and performance. AWS Elastic Beanstalk, along with other services like CloudFront and RDS, ensures an optimized and cost-effective application infrastructure.
