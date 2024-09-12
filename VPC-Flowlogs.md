# AWS Project with VPC Flow Logs

## Overview

This project demonstrates the configuration of VPC Flow logs to monitor network traffic, configuring an S3 bucket for log storage, and applying Network ACLs to block specific incoming IP addresses. Then we can review and analyse the logs stored in S3. 

This is a basic project with the aim of gaining hands on experience using some of AWS services like EC2, S3, VPC, VPC Flow logs, and Network ACLs. 

## Steps to Implement

### 1. Setup an S3 bucket
Steps taken with relevant screen shots.
1. Navigated to S3 dashboard and clicked on create new bucket.
2. Created a new general purpose bucket named 'p-flowlogs-bucket'
3. The remaining settings with left at default or recommended.
4. ![image](https://github.com/user-attachments/assets/ba78e193-1293-4a5b-8fcc-d11f28b2b2c3)
5. Review the success alert at the top, and also note the AWS region eu-north-1.
6. As we will later download the flow logs to analyse on our machine, we dont need an IAMrole between S3 and EC2.

### 2. Create VPC and two subnets in different Availability Zones.
Steps taken with relevant screen shots.
1. Navigated to VPC dashboard, Your VPCs, and Create VPC.
2. I then created a VPC with CIDR block 10.0.0.0/24
3. ![image](https://github.com/user-attachments/assets/67bd3d87-a520-4a0a-a2d9-1933e9aab13e)
4. I then navigated to subnets and created two subnets in seperate AZ zones. Two subnets in our VPC with different AZ zones means we are splitting the VPC network range into smaller subnets, each with their own CIDR block. We place subnets in different AZ zones for redudancy and high availability. So if one resource fails the others can still operate. 
5. The first subnet has subnet CIDR block 10.0.0.0/28. Giving 16ips (11 usable). Region is eu-north-1a.
6. I then made the second subnet, this time with subnet CIDR block 10.0.0.16/28, region eu-north-1b.
7. By default each subnet in a VPC is associated with the default NACL, which allows all inbound and outbound traffic. Later we will create a custom NACL to tighten security.
8. ![image](https://github.com/user-attachments/assets/48bfb73e-5952-4529-ad33-c80000f552ca)
9. By assigning different CIDR blocks, we avoid possibility of IP overlap between subnets. This makes it simpler when defining route tables or managing services like VPNs or Direct Connect. Isolated subnets is also a secure practice.

### 3. Launch an EC2 instance in our VPC.
Steps taken with relevant screen shots.
1. Navigated to EC2 dashbaord and launched a new instance.
2. I created a new rsa key pair called flowlogs_project.pem and saved it to my desktop.
3. For the network settings, I made a security group to allow ICMP and SSH traffic from my IP address.
4. I then made sure our EC2 was  in our new VPC, and the subnet for AZ1.
5. Auto-assign public IP on.
6. All other settings were left to free tier such as storage and instance type.

### 3. Configure NACLs
Steps taken with relevant screen shots.
1. NACLs act as a firewall at the subnet level. Controlling both inbound and outbound traffic from our subnets, enhancing security.
2. Navigated to VPC dashboard and under security tab, network ACLs, Create network ACL.
3. Here I created an NACL within our VPC created for this project.
4. I then went and edited the inbound and outbound rules of our NACL to allow ICMP traffic for our IP address. NACLs have an implicit deny all rule. We must add explicit allow rules. The inbound rule allows us to make the ping request from our local machine to EC2. The outbound rule allows the EC2 to send the ping response back to our local machine.
5. Next I then needed to associcate our two subnets we made earlier with this Network ACL. Under subnet associations tab I clicked Edit subnet associations.
6. Two subnets were available to select, our AZ1 and AZ2 subnet we made before. I selected them and hit save changes.
7. ![image](https://github.com/user-attachments/assets/bc3a841c-5ab5-4cbc-8d4f-b0c273ac6a6c)
8. Each subnet can only be associated with one NACL at a time. So now they are not associated with the default allow all.

### 3. Enable VPC Flow logs and sent to our S3 Bucket.
Steps taken with relevant screen shots.
1. Navigated to VPC, Your VPCs, and under our vpc 'p-flowlogs-vpc' select the Flow logs tab and Create flow log.
2. 




