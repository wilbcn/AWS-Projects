# AWS VPC Endpoints & S3 Access with EC2 Instances

This project demonstrates the configuration of a Virtual Private Cloud (VPC) with two EC2 instances, one public and one private. The public instance connects to S3 via the Internet Gateway, while the private instance securely accesses S3 using a VPC Endpoint, thus reducing the attack surface by not having internet access.

A similar project was completed via AWS Simulearn, as part of their skill builder exercises. The below screenshot showcases the AWS architecture. 
![IncreaseSecuritywithVPCEndpoints](https://github.com/user-attachments/assets/22feb6d4-7fe5-4e5c-a9e5-6cedfe3dfa45)

## Architecture explained

<explain>

### 1. Create a VPC.
The first step is to create a VPC, an isolated section of our network designed to enhance the security of our services.



### 2. Create 2 subnets in different availability zones.
Creating 2 subnets across different AZs ensures better fault tolerance and resilience for our resources.



### 3. Attach an internet gateway to the VPC


### 4. Configure the route tables
Creating route tables for our subnets in AZ1 and AZ2. AZ1 will be public, having its route table configured with the IGW. AZ2 will have its own route table, but no link to the internet.

The default local rule allows traffic within the VPC itself to communicate between subnets, ensuring that resources inside the VPC can communicate with each other (e.g., our public and private EC2 instances).

### 5. Deploy our EC2 Instances and their Security Groups
Now I configured 2 EC2 instances, one in the public subnet, and the other in the private subnet. 


<private ec2> no ssh traffic from ssh etc. Only inbound access from public ec2 address.

/32 *one ip only*






