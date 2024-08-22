# AWS Real Time Production Project: VPC, Auto Scaling Group, Bastion Host, and Application Load Balancer


This project outlines the steps to build a scalable web application on AWS using core services like VPC, Auto Scaling, Bastion Host, and an Application Load Balancer. The final deployment includes a simple Python web application running on EC2 instances managed by an Auto Scaling group, accessible via an Application Load Balancer.

## Prerequisites

Before you begin, ensure you have the following:

- An AWS account with necessary permissions to create VPCs, EC2 instances, Auto Scaling groups, and Load Balancers.
- SSH key pairs for securely accessing EC2 instances.
- Basic knowledge of AWS networking, EC2, and web server configuration.

## Project Architecture

The architecture includes:
- **VPC**: A Virtual Private Cloud with public and private subnets spread across two Availability Zones (us-east-1a and us-east-1b).
- **NAT Gateway**: Allows instances in private subnets to connect to the internet while remaining inaccessible from the outside world.
- **Auto Scaling Group**: Automatically manages EC2 instances, ensuring availability and scalability of the application.
- **Bastion Host**: A secure gateway for SSH access to instances in private subnets.
- **Application Load Balancer**: Distributes incoming web traffic across the EC2 instances in the Auto Scaling group.
- **ETC................**


![Screenshot 2024-08-23 005016](https://github.com/user-attachments/assets/2422c21e-9a2c-4b73-a252-32d855ff5108)





## 1. Create the VPC

To create a VPC with two Availability Zones, each with a public and private subnet, follow these steps:

1. **Open the VPC Console**:
   - Navigate to the Amazon VPC console at https://console.aws.amazon.com/vpc/.
   
2. **Create the VPC**:
   - Choose **Create VPC** from the dashboard.
   - For **Resources to create**, select **VPC and more**.
   - Configure the VPC settings:
     - **Name**: Provide a name for your VPC.
     - **IPv4 CIDR block**: Specify a suitable CIDR block (e.g., 10.0.0.0/16).
     - **Availability Zones**: Choose **2** (us-east-1a and us-east-1b).
     - **Subnets**: Create **2 public subnets** and **2 private subnets**.
     - **NAT Gateways**: Set **1 per AZ** to ensure high availability.
     - **VPC Endpoints**: Deselect the **S3 Gateway** option.
     - **DNS Options**: Enable DNS resolution but leave DNS hostnames disabled unless required.
   - Click **Create VPC** to finish.

## 2. Set Up Auto Scaling Group

Deploy your application in a scalable manner using an Auto Scaling group:

1. **Create a Launch Template**:
   - Go to the EC2 dashboard and select **Launch Templates**.
   - Create a new launch template using an Ubuntu AMI.
   - Specify the key pair to use for SSH access.
   - Choose the VPC and the security group that allows traffic on port **8000** and **SSH (22)**.
   
2. **Configure the Auto Scaling Group**:
   - Go to the **Auto Scaling Groups** section.
   - Create a new Auto Scaling group and associate it with the launch template.
   - Choose the private subnets in your VPC.
   - Set the minimum number of instances to **2** to ensure redundancy and availability.

## 3. Create a Bastion Host

To securely manage instances in private subnets, set up a Bastion Host:

1. **Launch an EC2 Instance**:
   - Go to the EC2 console and launch a new instance.
   - Select the same VPC and choose a public subnet.
   - Use the same key pair as the Auto Scaling instances for ease of access.
   - Enable **Auto-assign public IP** to allow internet access.
   
2. **Security Configuration**:
   - Ensure that the security group attached to the Bastion Host allows SSH (port 22) access from your IP.

## 4. Transfer Key Pair to Bastion Host

To allow SSH access to EC2 instances in private subnets:

1. **Open Terminal**:
   - Securely copy the private key used for the Auto Scaling instances to the Bastion Host:
     ```bash
     scp -i /path/to/key.pem /path/to/key.pem ec2-user@<Bastion-Host-Public-IP>:/home/ec2-user/
     ```

## 5. Login and Access Private EC2 Instances

Once the key pair is transferred:

1. **SSH into Bastion Host**:
   - Access the Bastion Host:
     ```bash
     ssh -i /path/to/key.pem ec2-user@<Bastion-Host-Public-IP>
     ```
   
2. **Access Private EC2 Instances**:
   - Obtain the private IP address of one of the EC2 instances from the Auto Scaling group.
   - SSH from the Bastion Host into the EC2 instance:
     ```bash
     ssh -i /path/to/key.pem ec2-user@<Private-EC2-Instance-IP>
     ```
   
3. **Deploy a Simple Web Application**:
   - On the EC2 instance, create a simple HTML file and set up a basic Python web server:
     ```bash
     echo "Hello, World!" > index.html
     python3 -m http.server 8000
     ```

## 6. Set Up Application Load Balancer

To evenly distribute traffic across your EC2 instances:

1. **Create the Load Balancer**:
   - Navigate to the **Load Balancers** section of the EC2 console.
   - Create a new Application Load Balancer.
   - Select the public subnets from your VPC.
   - Associate the same security group that allows HTTP traffic on port **8000**.

2. **Attach Auto Scaling Group**:
   - Register your Auto Scaling group with the Load Balancer to ensure traffic is directed to the EC2 instances.

## 7. Test and Verify

Your web application is now accessible through the public DNS of the Application Load Balancer. Visit the Load Balancer's DNS address in your browser to see your application in action.

## 8. Cleanup

To avoid incurring ongoing costs:

1. **Terminate EC2 Instances**:
   - Terminate the instances in the Auto Scaling group.
   
2. **Delete the NAT Gateway**:
   - Remove the NAT Gateway from each Availability Zone.
   
3. **Delete the Load Balancer**:
   - Navigate to the **Load Balancers** section and delete the Load Balancer.
   
4. **Delete the VPC**:
   - Once all resources are deleted, you can safely delete the VPC.

---

This enhanced README provides more context and details, making it easier for someone to understand and replicate your project. Let me know if you need any further modifications!
