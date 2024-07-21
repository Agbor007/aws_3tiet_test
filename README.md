## Three-Tier Web Architecture: Step-by-Step Process




![Three-Tier Architecture](https://avinash.s3.amazonaws.com/3tier.png)


Three-Tier Architecture using EC2, RDS, ALB, ACM, and Route 53
Project Description
This project outlines the deployment of a robust, scalable, and secure three-tier web application architecture on AWS, utilizing EC2 instances, RDS for database management, Application Load Balancers (ALB), AWS Certificate Manager (ACM), and Route 53 for domain management.
Real-World Application
In a real-world scenario, imagine an e-commerce website where users can browse products, place orders, and manage their accounts. This architecture ensures that the website is highly available, scalable to handle varying traffic loads, and secure against potential threats.
Components
1.	Web Server (Front-End)
o	Function: Displays the website to users and handles user interactions.
o	Example: Shows product pages and handles user sign-ups.
2.	Application Server (Business Logic)
o	Function: Processes user actions and manages business logic.
o	Example: Checks product availability and updates user accounts.
3.	Database Server (Data Storage)
o	Function: Stores and manages all application data.
o	Example: Maintains records of user accounts and order history.
Key AWS Services Used
1.	Amazon EC2: Hosts the web and application servers.
2.	Amazon RDS: Manages the relational database with MySQL or PostgreSQL.
3.	Application Load Balancer (ALB): Distributes incoming traffic across multiple EC2 instances for high availability.
4.	AWS Certificate Manager (ACM): Manages SSL/TLS certificates for secure communication.
5.	Route 53: Provides domain registration and DNS routing.



Deployment Steps
1.	VPC Creation: Set up a Virtual Private Cloud (VPC) with subnets, route tables, and an Internet Gateway (IGW) for networking.
2.	NAT Configuration: Create and attach a NAT gateway for private subnet internet access.
3.	Domain Registration: Register a domain using Route 53.
4.	IAM Roles and Security Groups: Create roles and security groups to control access and permissions.
5.	RDS Setup: Configure a MySQL database instance in a private subnet for secure data storage.
6.	Code Deployment: Clone the project repository, configure database connections, and upload application code to an S3 bucket.
7.	EC2 and ASG Setup: Launch EC2 instances for the application tier, create AMIs, and set up Auto Scaling Groups (ASG) for scalability.
8.	Load Balancers Configuration: Create ALBs to manage traffic to the web and application tiers.
9.	Route 53 Configuration: Set up DNS records to route traffic to the ALBs.
Real-World Example
For an e-commerce site like Alibaba.com
1.	Web Tier: Displays product listings and handles user interactions.
2.	Application Tier: Processes orders, manages inventory, and updates user accounts.
3.	Database Tier: Stores user data, order history, and product information.
By implementing this architecture, Alibaba.com ensures that:
•	The site can handle high traffic volumes efficiently.
•	User data is securely stored and managed.
•	The system is resilient to failures, ensuring high availability.
•	Secure communication is maintained through SSL/TLS certificates.
This setup not only enhances the performance and reliability of the e-commerce platform but also provides a scalable solution to accommodate future growth.
Prerequisites for this implementation
•	Visual Studio Code
•	AWS Management Console
•	PuTTY

This project is not limited based on what we will implement as you can modify and add services or less to make it respect the well architectural framework.
Step 1: VPC Creation
1.	Create a VPC:
o	Use the “VPC and more” option to create your VPC, subnets, Route Tables, and IGW.
o	Do not create a NAT at this stage (I want you to manually create you NAT).
2.	Create Subnets:
o	Create six subnets across two Availability Zones (AZs).
3.	Create and Attach NAT:
o	Manually create a NAT and attach it to your Private App-Tier.
o	Name it, select your App-Tier AZ, set the connectivity type to Public, allocate an Elastic IP, tag it if desired, and create.
o	Verify if the Elastic IP has been created.
4.	Create Route Tables:
o	Create five route tables and associate them as follows:
	One for both public subnets
	Four for the private subnets (App-Tier 1, App-Tier 2, DB-Tier 1, DB-Tier 2)
	Optionally, use one Route Table for App-Tier and one for DB-Tier and delete the other two.
5.	Modify Route Table Associations:
o	Associate the two App-Tier subnets with one route table (App-Tier-RT) and the two DB-Tier subnets with another route table (DB-Tier-RT).
o	Edit the App-Tier-RT routes:
	Add a route with destination 0.0.0.0/0 and target set to the NAT created above.
Step 2: Domain Name Registration
•	Register a domain name via Route 53 if you don’t have one.
Step 3: IAM Roles and Security Groups
1.	Create IAM Role:
o	Create a role under IAM with the use case set to EC2.
o	Attach the SSMFullAccess policy.
2.	Create Security Groups:
o	Web-Tier SG
o	Load Balancer SGs (for Internal and External ALBs)
o	App-Tier SG
o	DB-Tier SG
o	Verify the allowed protocols for each security group.
NB: Visit the last page to have an update of the protocols needed. Improvise if need be.
3.	Create NACLs:
o	Create three NACLs for public subnets (Web-Tiers), App-Tiers, and DB-Tiers.
Step 4: RDS Setup
1.	Create RDS Subnet Group:
o	Attach it to a VPC and select your DB-Tier subnets.
2.	Create a Database:
o	Use the MySQL engine.
o	Modify the DB Instance Identifier and Master username as needed.
o	Insert and securely store your master password.
o	Enable Multi-AZ deployment if desired.
o	Choose the free tier for cost reduction if applicable.
o	Use self-managed credential management.
o	Select your instance and storage settings.
o	Select your SG, VPC, and make the DB private.
o	Leave other settings as default and create the database.

Part 2: Code, AMI, ASG, ELB, and S3 Setup
1.	Clone Repository:
o	Clone the repository from this link to your Git account or download it locally.
o	Do not work directly from the provided Git account.
2.	Database Configuration:
o	Access the created database and copy the DB endpoint name.
o	Edit your DbConfig code in Visual Studio Code to include the endpoint name.

3.	Create S3 Bucket:
o	Upload the cloned/downloaded application code folder to a private S3 bucket.
EC2 Creation and Configuration for App-Tier
1.	Launch EC2 Instance:
o	Launch a Linux or Ubuntu EC2 instance and connect to it (Ubuntu was used in this deployment).
o	Attach the IAM role created earlier.
o	Use Session Manager or any remote connection type to connect.
o	Follow the configuration document to set up your App-Tier server.
o	Update commands as necessary, ensuring all S3 bucket names are correct.
o	Stop the instance and create an AMI-Apptier from it.
o	Delete the EC2 instance after creating the AMI.
2.	Create ASG Launch Template for your App-Teir:
o	Use the created AMI-Apptier and configure the instance type and keypair.
o	Do not include any subnet at this point.
3.	Create ASG:
o	Name it, select the launch template, VPC, and two App-Tier subnets.
4.	Create ALB for App-Tier:
o	Create a target group for the ALB 
o	Set the target type to pointing to the ASG-Apptier and the health check path to /health.
o	Use HTTP port 4000.
o	Create.

o	Create the ALB and associate it with the two App-Tier subnets.
o	Set the listener port to 80 (HTTP).
o	Select the security group for Internal ELB.
o	Copy the ALB DNS name and update your Nginx.conf code in the S3 bucket, then reupload it.
 
Web Tier Setup
1.	Launch EC2 Instance:
o	Launch an EC2 instance in any Web-Tier subnet and attach the IAM role.
o	Follow the Web-Tier installation process in the “Implementation_Steps” file.
o	Terminate the Web-Tier EC2 instance and create an AMI-WebTier from it.
o	Create Launch Template-2 and ASG-WebTier for the Web-Tier EC2 in the public subnet.
o	Create a Target group-2 for Web-Tier ALB.
2.	Create Web-Tier ELB:
o	Follow the steps to create an ALB-2 for the Web-Tier subnet.
o	Set the scheme to Internet-facing.
o	Use HTTPS/443 for the protocol.
o	Load the ACM certificate obtained during domain name registration.
o	Optionally enable WAF.
o	Create the ALB.
Route 53 Configuration
1.	Setup Route 53:
o	Access your Route 53 dashboard and open your hosted zone.
o	Create a record, enabling Alias to link it to an Internet facing ALB.
o	Choose the Internet facing ALB and set the routing policy to Simple or any preferred policy.
2.	Verify:
o	Copy and paste your domain name into a browser to verify the setup.
END













List of protocols needed for your firewalls. 
Remember to adjust the Security group for your External and Internal Load balancers
Security Groups
Web Tier (Public Subnet)
•	HTTP (Port 80):
•	HTTPS (Port 443):
•	SSH (Port 22):
•	All TCP: 
All pointing to Internet facing ALB SG
Application Tier (Private Subnet)
•	HTTP (80) and HTTPS
•	Custom Application Port (4000):
•	MySQL/Aurora (Port 3306) or PostgreSQL (Port 5432):
•	All pointing to Internal ALB SG
Database Tier (Private Subnet)
•	MySQL/Aurora (Port 3306) or PostgreSQL (Port 5432):
o	Source: Application Tier Security Group (Allow traffic from the Application Tier)
Internet facing ALB SG
•	HTTP (Port 80):
•	HTTPS (Port 443):
•	All TCP: 
o	All pointing to 0.0.0.0/0
•	SSH (Port 22): from MyPC


Internal ALB SG
•	All Traffic pointing to Web-Tier SG
Network ACLs (NACLs)
Public Subnet (Web Tier)
•	Inbound Rules:
o	Allow HTTP (Port 80) from 0.0.0.0/0
o	Allow HTTPS (Port 443) from 0.0.0.0/0
o	Allow SSH (Port 22) from 0.0.0.0/0
o	Allow MySQL from 0.0.0.0/0
•	Outbound Rules:
o	Allow HTTP (Port 80) to 0.0.0.0/0
o	Allow HTTPS (Port 443) to 0.0.0.0/0
o	Allow SSH (Port 22) from 0.0.0.0/0
o	Allow MySQL from 0.0.0.0/0
Private Subnet (Application Tier)
•	Inbound Rules:
o	Allow HTTP (Port 80) from your VPC CDIR
o	Allow HTTPS (Port 443) from your VPC CDIR
o	Allow SSH (Port 22) from your Web-Tier SG
o	Allow MySQL from your VPC CDIR
o	Allow Custom (4000) from your VPC CDIR
•	Outbound Rules:
o	Allow HTTP (Port 80) from your VPC CDIR
o	Allow HTTPS (Port 443) from your VPC CDIR
o	Allow SSH (Port 22) from your Web-Tier SG
o	Allow MySQL from your VPC CDIR
o	Allow Custom (4000) from your VPC CDIR
Private Subnet (Database Tier)
•	Inbound Rules:
o	Allow MySQL/Aurora (Port 3306) or PostgreSQL (Port 5432) from the Application Tier subnet
•	Outbound Rules:
o	Allow MySQL/Aurora (Port 3306) or PostgreSQL (Port 5432) from the Application Tier subnet

o	Allow traffic to the Application Tier subnet (typically ephemeral ports 1024-65535)

#https://youtu.be/lh5f69t7L9A?si=B5vBdoszcYeh4Is_

