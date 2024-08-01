# AWS VPC Peering Project

## Overview

This project demonstrates the configuration of VPC Peering in AWS to create isolated network segments with specific connectivity requirements.

Objective: To set up VPC Peering between three VPCs (Marketing, Finance, and Developer) such that:

Marketing and Developer EC2 instances can access the Finance EC2 instance.
No direct communication exists between Marketing and Developer VPCs.

For security related purposes, and to not incur added costs, all instances of other services were removed after this project. This is a basic project with the aim of gaining hands on experience using various AWS services such as VPC & EC2, as well exploring networking options within these services like subnets, routing tables, and setting inbound rules.
## Architecture Diagram

This screenshot was taken from AWS Simulated Learning. I then recreated what I learned in my own AWS account, reflected in this project.
![Architecture Diagram](https://github.com/wilbcn/pngs.vpcs.practise/blob/main/ConnectingVPCs.png)

## Steps to Implement

### 1. Create VPCs
Create 3 VPCs for each department: Marketing, Finance, Developer.

Steps Taken:
1. Navigate to VPC on AWS, under Virtual Private Cloud drop down menu click Your VPCs.
2. Create new VPC button.
3. Configure the first VPC for Marketing.
![Marketing VPC configuration](https://github.com/wilbcn/pngs.vpcs.practise/blob/main/screenshot_of_VPC1.png)
4. Click create VPC, and review the status: Available
![3 seperate VPCs](https://github.com/wilbcn/pngs.vpcs.practise/blob/main/screenshot_of_3vpcs.png)
5. Each VPC has a different IPv4 CIDR block for proper routing and to avoid overlapping IP ranges.

### 2. Create Subnets
Create a subnet for each departments VPC.

Steps taken:
1. Navigate to VPC on AWS, under Virtual Private Cloud drop down menu click Subnets.
2. Add the first subnet for Marketing department VPC.
![Marketing VPC subnet](https://github.com/wilbcn/pngs.vpcs.practise/blob/main/screenshot_of_subnet1.png)
3. Repeat these steps for the other two departments, adding each IPv4 CIDR block and matching subnet.
![Overview of the subnets](https://github.com/wilbcn/pngs.vpcs.practise/blob/main/screenshot_of_3subnets.png)
4. Now we have a subnet for each department.

### 3. Launch EC2 Instances
Create 3 seperate EC2 Instances. One for each department: Marketing, Finance, Developer.
Finance instance should only be accessible via private IP address, limiting exposure.
Marketing/Developer instances will require ssh set to allowed. 

Steps Taken:
1. Navigate to EC2 on AWS and Launch Instance.
2. I named our first instance "connecting-vpc/MarketingServer" and selected AMI Amazon Linux.
3. Instance type was left as free tier and no key pair needed. We will only be using ssh connect via AWS for this project.
4. Then by expanding on network settings, I added our Marketing VPC and subnet.
5. Under network settings, I added inbound rules for the region based (eu-north-1) IP address for EC2 Connect. Found here: https://ip-ranges.amazonaws.com/ip-ranges.json
![Region inbound rule](https://github.com/wilbcn/pngs.vpcs.practise/blob/main/screenshot_of_marketinginbound.png)
6. Free Tier storage was applied
7. Instance was launched
8. In order to connect to our instance, there is a few more steps I must take. I navigated back to VPC and internet gateways, and created a new internet gateway and attached it to our marketing VPC. 
![Adding lgw to our VPC for marketing](https://github.com/wilbcn/pngs.vpcs.practise/blob/main/screenshot_igw_marketing1.png)
9. I then added this igw into our route table for our Marketing EC2 instance.
![Route table igw](https://github.com/wilbcn/pngs.vpcs.practise/blob/main/screenshot_marketing_routetable_igw.png)
10. I tried a quick ping from our Finance server from Marketing via ssh connect, but as expected this failed.
![Pinging Finance from Marketing](https://github.com/wilbcn/pngs.vpcs.practise/blob/main/screenshot_of_marketing_ping1.png)


### 4. Create VPC Peering Connections

1. **Marketing VPC <-> Finance VPC:**
   - Go to the **VPC Peering Connections** section.
   - Create a new peering connection between Marketing and Finance VPCs.
2. **Developer VPC <-> Finance VPC:**
   - Create another peering connection between Developer and Finance VPCs.
3. **Ensure No Peering Connection:**
   - Confirm that there is no peering connection between Marketing and Developer VPCs.

### 5. Update Route Tables

1. **Marketing VPC Route Table:**
   - Add a route to the Finance VPC CIDR block (`10.1.0.0/16`) via the peering connection with Finance.
2. **Finance VPC Route Table:**
   - Add routes to:
     - Marketing VPC CIDR block (`10.0.0.0/16`) via the peering connection with Marketing.
     - Developer VPC CIDR block (`10.2.0.0/16`) via the peering connection with Developer.
3. **Developer VPC Route Table:**
   - Add a route to the Finance VPC
