# AWS Real Time Production Project: VPC, Auto Scaling Group, Bastion Host, and Application Load Balancer


This example demonstrates how to create a VPC that you can use for servers in a production environment. To improve resiliency, you deploy the servers in two Availability Zones, by using an Auto Scaling group and an Application Load Balancer. For additional security, you deploy the servers in private subnets. The servers receive requests through the load balancer. The servers can connect to the internet by using a NAT gateway. To improve resiliency, you deploy the NAT gateway in both Availability Zones.

Contents
Overview
1. Create the VPC
2. Deploy your application
3. Test your configuration
4. Clean up

Overview

The following diagram provides an overview of the resources included in this example. The VPC has public subnets and private subnets in two Availability Zones. Each public subnet contains a NAT gateway and a load balancer node. The servers run in the private subnets, are launched and terminated by using an Auto Scaling group, and receive traffic from the load balancer. The servers can connect to the internet by using the NAT gateway. The servers can be using a gateway VPC endpoint.

AWS Project: VPC, Auto Scaling Group, Bastion Host, and Application Load Balancer
This project demonstrates the setup of an AWS infrastructure using VPC, Auto Scaling, Bastion Host, and an Application Load Balancer to deploy and run a Python application.

1. Create the VPC
To create a VPC with two Availability Zones (us-east-1a and us-east-1b) and a NAT gateway in each zone, follow these steps:

Open the Amazon VPC console at https://console.aws.amazon.com/vpc/.
On the dashboard, choose Create VPC.
For Resources to create, choose VPC and more.
Configure the VPC:
For Name tag auto-generation, enter a name for the VPC.
For IPv4 CIDR block, enter a suitable CIDR block.
For Number of Availability Zones, choose 2.
For Number of public subnets, choose 2.
For Number of private subnets, choose 2.
For NAT gateways, choose 1 per AZ.
For VPC endpoints, deselect S3 Gateway.
Choose Create VPC.

2. Create the Auto Scaling Group
   
Deploy your application using an Auto Scaling group by following these steps:

Create a launch template:
Use an Ubuntu AMI.
Select the key pair you want to use.
Choose the VPC created in the previous step.
Set up a security group that allows traffic on ports 8000 and SSH (22).
Create the Auto Scaling group:
Use the launch template created above.
Set the group to launch instances in the private subnets.
Set the minimum number of instances to 2.

3. Create a Bastion Host
   
Set up a Bastion Host in the public subnets:

Launch an EC2 instance:
Use the same VPC.
Choose a public subnet.
Select the key pair used for the Auto Scaling instances.
Enable Auto-assign public IP.

4. Secure Copy Key Pair to Bastion Host
   
Open your terminal.
Securely copy the key pair used for the Auto Scaling EC2 instances to the Bastion Host.

6. Login to the Bastion Host and Access EC2 Instances
   
SSH into the Bastion Host from your terminal.
Copy the private IP address of one of the EC2 instances in the Auto Scaling group.
SSH from the Bastion Host into the EC2 instance using the private IP address.
Once logged in, create a simple HTML application.

8. Run Python Application
   
On the EC2 instance, run your Python application:
bash
Copy code
python3 -m http.server 8000

10. Create Application Load Balancer
    
Set up an Application Load Balancer on the public subnet:

Open the EC2 console and navigate to Load Balancers.
Create a new Application Load Balancer:
Use the same VPC.
Select the public subnets.
Use the security group created earlier that allows HTTP traffic on port 8000.
Attach the Auto Scaling group to the Load Balancer.

Successful Deployment
Your Python application should now be successfully running behind an Application Load Balancer, accessible via the public IP of the Load Balancer.

