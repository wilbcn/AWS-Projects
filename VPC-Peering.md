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

### 3. Launch EC2 Instances & Create VPC
Create 3 seperate EC2 Instances. One for each department: Marketing, Finance, Developer.
Finance instance should only be accessible via private IP address, limiting exposure.
Marketing/Developer instances will require ssh set to allowed. 

Steps Taken:
1. Navigate to EC2 on AWS and Launch Instance.
2. I named our first instance "connecting-vpc/MarketingServer" and selected AMI Amazon Linux.
3. Instance type was left as free tier and no key pair needed. We will only be using ssh connect via AWS for this project.
4. Then by expanding on network settings, I added our Marketing VPC and subnet. Auto assign public IP was Enabled.
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
11. I then repeated these steps for the Developer server. Taking careful steps to add internet gateway access, appropriate inbound rules etc.
12. The Finance Instance had to be created differently. Auto assign public IP was disabled, as for tighter security we want to access this only via the private IP address. 
13. Inbound rules were set to allow ssh from the Marketing and Developer servers private IPs only.
![Inbound rules for Finance](https://github.com/wilbcn/pngs.vpcs.practise/blob/main/screenshot_inboundrules_finance.png)
14. Now I navigated to VPC, Peering connections, and create peering connection.
15. Local VPC was set to Marketing, and the accepter is Finance VPC.
![Marketing -> Finance Peering](https://github.com/wilbcn/pngs.vpcs.practise/blob/main/Marketing2Finance_Peering.png)
16. Reviewing the status, which is pending acceptance. I clicked on Actions and accept request. The status was then updated to Active.
![Accepting the Peering Request](https://github.com/wilbcn/pngs.vpcs.practise/blob/main/Market2finance_peering_complete.png)
17. I repeated this step for Developer and Finance.
18. Next step was to add these Peering connections in the Route tables for both Marketing and Finance. The destination will be the private IP of our Finance Server. PCX was the relevant/created VPC for that server Marketing/Developer.
![Route table with Peering for Marketing](https://github.com/wilbcn/pngs.vpcs.practise/blob/main/MarketingRouteTableFinal.png)
19. I also did the same for Developer server.
20. Now I must also edit the route tables on the Finance Server. Here I added the CIDR block for both Marketing and Finance into the route table for Finance, and selected the relevant PCX
![Finance Route Table](https://github.com/wilbcn/pngs.vpcs.practise/blob/main/financeroutetablefinal.png)

### 4. Test the connections

