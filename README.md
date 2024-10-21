# VPC Peering

In this tutorial, we’ll walk through connecting two EC2 instances located in separate private subnets in different VPCs using VPC Peering. The setup ensures secure communication between the two instances without exposing them to the public internet.

## Network Setup

**VPC 1 (for 2 EC2):**
VPC CIDR: 10.1.0.0/16
Subnet: 10.1.1.0/24 (Public Subnet)
Subnet: 10.1.2.0/24 (Private Subnet)

**VPC 2 (for EC2–2):**
VPC CIDR: 10.2.0.0/16
Subnet: 10.2.2.0/24 (Private Subnet)

## Step 1: Create Two VPCs and Subnets

**Create VPC 1:**
Go to VPC in the AWS Management Console.
Click on Create VPC, set the CIDR block to 10.1.0.0/16, and name it VPC-1.
Create a subnet for VPC-1 with CIDR 10.1.1.0/24 and name it Public Subnet 1.
Create a subnet for VPC-2 with CIDR 10.1.2.0/24 and name it Private Subnet 1.
**Create VPC 2:**
Follow the same steps for VPC-2 using CIDR 10.2.0.0/16.
Create a subnet within VPC-2 with CIDR 10.2.2.0/24 and name it Private Subnet 2.

## Step 2: Launch EC2 Instances in Private Subnets

**Launch Bastion in VPC 1:**
Go to EC2 -> Launch Instance.
Select VPC-1 and choose Public Subnet 1 as the network.
Select Auto-assign Public IP.
Launch the instance. (for example private ip is: 10.1.1.10, public ip is: 45.34.83.44)

**Launch EC2 -1 in VPC 1:**
Go to EC2 -> Launch Instance.
Select VPC-1 and choose Private Subnet 1 as the network.
Disable Auto-assign Public IP.
Launch the instance and assign the private IP 10.1.2.11.

**Launch EC2–2 in VPC 2:**
Repeat the above steps for VPC-2 and the private IP 10.2.2.22 (for example) assigned to the instance.

## Step 3: Set Up VPC Peering

**Create the Peering Connection:**
In the VPC Dashboard, go to Peering Connections and click Create Peering Connection.
Select VPC-1 as the requester and VPC-2 as the accepter.
Click Create Peering Connection.

**Accept the Peering Request:**
Once the peering connection is created, accept the request by going to Peering Connections.
Select the request and click Accept.

## Step 4: Update Route Tables for VPCs

**Update Route Table for VPC-1:**
Go to Route Tables in the VPC Dashboard.
Edit the route table for Public Subnect 1 and Private Subnet 1.
Add a route with destination 10.2.0.0/16 (CIDR of VPC-2) and select the peering connection as the target.

**Update Route Table for VPC-2:**
Edit the route table for Private Subnet 2.
Add a route with destination 10.1.0.0/16 (CIDR of VPC-1) and select the peering connection as the target.

## Step 5: Configure Security Groups

**Update Security Group for EC2–1:**
In the EC2 Dashboard, go to Security Groups.
Add an inbound rule allowing traffic from 10.2.0.0/16 (VPC-2) for either all traffic or specific protocols (e.g., SSH and HTTP).

**Update Security Group for EC2–2:**
Similarly, update the security group for EC2–2 to allow SSH traffic from 10.1.0.0/16 (VPC-1).

## Step 6: Setup and Test the Connection

SSH into EC2–1 (through a bastion if needed).
Install Nginx.
Then through Bastion access EC2–2
Test the connection using curl <http://10.1.2.11> (EC2–1’s private IP)
Nginx Page will be displayed
That’s it! You’ve successfully set up VPC Peering between two private subnets, allowing EC2 instances to communicate securely.
