# AWS VPC Endpoints & S3 Access with EC2 Instances

This project demonstrates the configuration of a Virtual Private Cloud (VPC) with two EC2 instances, one public and one private. The public instance connects to S3 via the Internet Gateway, while the private instance securely accesses S3 using a VPC Endpoint, thus reducing the attack surface by not having internet access.

A similar project was completed via AWS Simulearn, as part of their skill builder exercises. The below screenshot showcases the AWS architecture. 
![IncreaseSecuritywithVPCEndpoints](https://github.com/user-attachments/assets/22feb6d4-7fe5-4e5c-a9e5-6cedfe3dfa45)

## Architecture explained

<explain>

### 1. Create a VPC.
The first step is to create a VPC, an isolated section of our network designed to enhance the security of our services.
1. VPC Dashboard, Your VPCs, Create VPC
2. Add the appropriate IPv4 CIDR Block. (something that is not in use so there is no overlap)
<img width="1163" alt="image" src="https://github.com/user-attachments/assets/f41fea04-173f-462a-8b47-f48736accea5">


### 2. Create 2 subnets in different availability zones.
Creating 2 subnets across different AZs ensures better fault tolerance and resilience for our resources.
1. In the VPC dashboard, on the left panes go to Subnets, Create subnet.
2. Create 2 subnets in seperate AZ zones, selecting our new VPC, but dividing each subnet into different blocks within our VPC.
<img width="1157" alt="image" src="https://github.com/user-attachments/assets/f05cf846-7f2a-4bfe-87c4-f8165e02ab09">

### 3. Attach an internet gateway to the VPC
Creating an internet gateway so that our public EC2 instance has access from the internet. The default VPC already has IGW access, but our new custom VPC does not. When you create your own custom VPC, you have complete control over how your network is architected. This includes control over subnets, route tables, security, and internet access.
1. In the VPC dashboard, go to internet gateways, Create internet gateway.
2. Next using the Actions button, we can attach this to our custom VPC.
<img width="1161" alt="image" src="https://github.com/user-attachments/assets/f6ff684e-1ada-47db-9d49-c0ea6af9fa41">

### 4. Configure the route tables
Creating route tables for our subnets in AZ1 and AZ2. AZ1 will be public, having its route table configured with the IGW. AZ2 will have its own route table, but no link to the internet.
1. In VPC dashboard, go to route tables on the left pane, and Create route table.
2. Name each route table for public/private.
3. Under the routes tab for public, edit the routes and add our target internet gateway.
<img width="1362" alt="image" src="https://github.com/user-attachments/assets/0265346d-6d00-4c9d-9543-da47be68cb7f">
The default local rule allows traffic within the VPC itself to communicate between subnets, ensuring that resources inside the VPC can communicate with each other (e.g., our public and private EC2 instances).
4. Add a route table for the private subnet, without adding any internet access.
5. In Subnet associations, set the public/private route tables retrospectively with our public/private subnets (AZ1/AZ2).
<img width="1165" alt="image" src="https://github.com/user-attachments/assets/424bcb3a-bd00-4e9c-afb3-2e8ce64083ef">


### 5. Deploy our EC2 Instances and their Security Groups
Now I configured 2 EC2 instances, one in the public subnet, and the other in the private subnet. 


<private ec2> no ssh traffic from ssh etc. Only inbound access from private address of public ec2.

/32 *one ip only*


### 6. Create an S3 bucket
Creating an S3 bucket in the same region as our VPC and EC2 instances.
1. 

### 7. Create a VPC endpoint for S3 access from the private instance
Creating a VPC Endpoint that allows our private EC2 instance to securely connect to S3 over the AWS network, bypassing the need for an Internet Gateway or NAT.
1.

### 8. Create a new IAM role for S3 Access from EC2
Attaching read only access to our EC2 instances for S3.

### 9. Test S3 Access from both instances
Logging into our public ec2 from our local machine via ssh and peforming tests. Then ssh to private ec2 from the public instance and confirm permissions also.








