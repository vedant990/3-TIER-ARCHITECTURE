# Three-Tier Architecture System

## System Components

- **Web Server:** A publicly accessible server connected to the internet.
  
- **Application Server:** An internal-facing server accessible only through the Web Server.

- **Database Server:** Accessible exclusively through the Application Server, not publicly accessible.

## Basic Implenation Outline

### Virtual Private Cloud (VPC)

A **Virtual Private Cloud (VPC)** provides a secure, isolated network environment within the cloud. It empowers you to deploy and manage cloud services while maintaining control over security and scalability.

### Subnets

**Subnetting** involves dividing a larger IP network into smaller, more manageable subnetworks. Our architecture will utilize subnets to organize and secure our components effectively.

#### Subnet Configuration

We'll need the following subnets to structure our system:

- **2 Public Subnets for Web Layer:** These subnets are accessible from the internet, serving as the entry point to our system.

- **2 Private Subnets for Application Layer:** Reserved for internal-facing servers and middleware, enhancing security and control.

- **2 Subnets for Database Layer:** These subnets house our databases, ensuring data security and controlled access.

By implementing this subnetting strategy, we create a robust, secure, and well-organized Three-Tier Architecture System.


### NAT Gateway

A **NAT Gateway**, or Network Address Translation Gateway, plays a crucial role in our system:

#### NAT Gateway Configuration

**Configuration Steps:**

1. **Create a NAT Gateway:** In your AWS (or similar cloud provider) console, create a NAT Gateway in a public subnet. Ensure it has an Elastic IP assigned.

2. **Route Table for Private Subnets:** Modify the Route Table associated with your private subnets to route traffic through the newly created NAT Gateway.

3. **Security Groups:** Adjust the rules to allow outbound traffic from your Application Servers to the NAT Gateway's Elastic IP.

**Purpose:** NAT Gateway provides outbound internet connectivity for resources in private subnets, enhancing security and control.

### Route Tables

**Route Tables** are pivotal in determining how traffic is routed within our Virtual Private Cloud (VPC):

#### Route Table Configuration

**Configuration Steps:**

1. **Identify Subnets:** Determine which subnets are public, private, and related to the Database Layer.

2. **Create Route Tables:** Create separate route tables for each subnet category (public, private, database).

3. **Associate Subnets:** Associate the appropriate subnets with their corresponding route tables.

4. **Set Routes:** Configure routes in each route table:
   - **Public Subnets:** Set the default route to the Internet Gateway.
   - **Private Subnets:** Set the default route to the NAT Gateway.
   - **Database Layer:** Set routes to allow traffic only from Application Servers.


### Launch Templates

**Launch Templates** define the instance configuration for your resources. In our architecture, we require two launch templates, one for instances in the public subnet and another for instances in the private subnet.

#### Launch Template Configuration

**Public Subnet Launch Template:**

- **Instance Configuration:** Define the instance type, key pair for SSH access, and user data for configuration.

- **Security Group:** Attach a security group allowing HTTPS and SSH from anywhere.

**Private Subnet Launch Template:**

- **Instance Configuration:** Similar to the public subnet, but SSH access should be controlled.

- **Security Group:** Attach a security group allowing SSH connected to the public subnet security group, ensuring controlled SSH access.

### Security Groups

**Security Groups** control inbound and outbound traffic to and from your instances. We need to configure them as follows:

- **Public Subnet Security Group:** Allow HTTPS and SSH traffic from anywhere (0.0.0.0/0).

- **Private Subnet Security Group:** Allow SSH traffic attached to the public subnet security group for controlled access.

- **Database Security Group:** Configure this group to allow traffic only from the Application Servers, ensuring database security


### Auto Scaling

**Auto Scaling** automates adding or removing instances based on demand. Here's how to set it up for public web and private app instances:

#### Public Web Auto Scaling Group Configuration

1. **Name:** Create an Auto Scaling group named "PublicWeb."

2. **Launch Template:** Select the launch template for instances in the public subnet.

3. **VPC:** Choose the VPC specifically created for your Three-Tier Architecture.

4. **Availability Zones:** Select the availability zones relevant to your deployment.

Code to add in User data :
```
 #!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<html><body><h1>Web Tier Success!</h1></body></html>" > /var/www/html/index.html
```


#### Private App Auto Scaling Group Configuration

1. **Name:** Create an Auto Scaling group named "PrivateApp."

2. **Launch Template:** Choose the launch template for instances in the private subnet.

3. **VPC:** Select the VPC dedicated to your Three-Tier Architecture.

4. **Availability Zones:** Choose the appropriate availability zones for your deployment.


### Amazon RDS (Relational Database Service)

To complete your architecture, create an Amazon RDS instance for your database while staying within the free tier limits. Follow these steps:

#### RDS Configuration

1. **Database Engine:** Choose the database engine that suits your application (e.g., MySQL, PostgreSQL).

2. **DB Instance Class:** Select a DB instance class eligible for the free tier. Be mindful of the instance storage included in the free tier.

3. **DB Instance Identifier:** Provide a unique name for your RDS instance.

4. **Master Username and Password:** Create a database username and master password. Store these securely as they will be needed in your application configuration.

5. **VPC:** Select the VPC specifically created for your Three-Tier Architecture.

6. **Availability Zone:** Choose an availability zone consistent with your architecture's design.

## Architecture Diagram
![unnamed](https://github.com/vedant990/3-TIER-ARCHITECTURE/assets/121371568/4c6bcbd2-416d-4cfc-8e0e-6d289a943dc2)




## Result
