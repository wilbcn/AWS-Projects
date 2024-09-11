# AWS Project with VPC Flow Logs

## Overview

This project demonstrates the configuration of VPC Flow logs to minitor network traffic, configuring an S3 bucket for log storage, and apply Network ACLs to block specific incoming IP addresses. Then we can review and analyse the logs stored in S3. 

This is a basic project with the aim of gaining hands on experience using some of AWS services like S3, VPC, VPC Flow logs, and Network ACLs. 

## Steps to Implement

### 1. Setup an S3 bucket
Steps taken with relevant screen shots.
1. Navigated to S3 and clicked on create new bucket.
2. Created a new general purpose bucket named 'p-flowlogs-bucket'
3. The remaining settings with left at default or recommended.
4. ![image](https://github.com/user-attachments/assets/ba78e193-1293-4a5b-8fcc-d11f28b2b2c3)
5. Review the success alert at the top, and also note the AWS region eu-north-1.


### Create and setup a VPC
Steps taken with relevant screen shots.
1. Navigated to VPC, Your VPCs, and Create VPC.
2. I then created a VPC with CIDR block 10.0.0.0/24
3. ![image](https://github.com/user-attachments/assets/67bd3d87-a520-4a0a-a2d9-1933e9aab13e)
4. I then navigated to subnets and created two subnets in seperate AZ zones. Two subnets in our VPC with different AZ zones means we are splitting the VPC network range into smaller subnets, each with their own CIDR block. We place subnets in different AZ zones for redudancy and high availability. So if one resource fails the others can still operate. 
5. The first subnet has subnet CIDR block 10.0.0.0/28. Giving 16ips (11 usable). Region is eu-north-1a.
6. I then made the second subnet, this time with subnet CIDR block 10.0.0.16/28, region eu-north-1b.
7. ![image](https://github.com/user-attachments/assets/c05c2ede-c6c6-40f8-ae76-f76b3c6b6926)
8. By assigning different CIDR blocks, we avoid possibility of IP overlap between subnets. This makes it simpler when defining route tables or managing services like VPNs or Direct Connect. Isolated subnets is also a secure practice.
9. 


