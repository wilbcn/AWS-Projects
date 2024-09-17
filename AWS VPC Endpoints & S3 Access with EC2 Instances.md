# AWS VPC Endpoints & S3 Access with EC2 Instances

This project demonstrates the configuration of a Virtual Private Cloud (VPC) with two EC2 instances, one public and one private. The public instance connects to S3 via the Internet Gateway, while the private instance securely accesses S3 using a VPC Endpoint, thus reducing the attack surface by not having internet access.

A similar project was completed via AWS Simulearn, as part of their skill builder exercises. The below screenshot showcases the AWS architecture. 
![IncreaseSecuritywithVPCEndpoints](https://github.com/user-attachments/assets/22feb6d4-7fe5-4e5c-a9e5-6cedfe3dfa45)

## Architecture explained
This project demonstrates a secure AWS network architecture. We will setup and configure a VPC, public and private subnets, and an S3 VPC Endpoint. This project aims to showcase how we can reduce the attack surface by limiting access for the private EC2 instance, while maintaining some level of access through our public instance. 

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
1. In the EC2 dashboard, Launch a new instance
2. Name it and use the Amazon Linux AMI.
3. All settings were kept at the free tier.
4. Generate a key pair and save locally.
5. Expand the network settings, choosing our custom VPC, and for our first instance select the public subnet AZ1.
6. Auto-assign public IP was enabled, as we are later going to connect to the public ip address. This first instance is public facing.
7. Create a new security group, allowing ssh traffic from our local machine.
8. Launch instance
9. I then repeated these steps for the private EC2 instance, with some settings changed.
10. We generated another key pair.
11. We disabled auto-assign public IP, so we will only have a private ipv4 address.
12. We created a security group that allows traffic only from the private ip address of the public instance.
13. This private instance was put in our custom VPC, but in the private subnet AZ2, that has no internet gateway.

### 6. Create an S3 bucket
Creating an S3 bucket in the same region as our VPC and EC2 instances.
1. Navigate to S3 dashboard and Create new bucket.
2. Create a new general purpose bucket, blocking all public access enabled.
3. All other settings were kept as default. Our S3 will be used later for testing access and not much else in this project.
<img width="1287" alt="image" src="https://github.com/user-attachments/assets/ba264586-fc3e-4afb-b705-220e715fd5d2">

### 7. Create a VPC endpoint for S3 access from the private instance
Creating a VPC Endpoint that allows our private EC2 instance to securely connect to S3 over the AWS network, bypassing the need for an Internet Gateway or NAT.
1. Navigate to the VPC dashboard, and endpoints, Create endpoint.
2. Give it a name tag and select AWS Services.
3. Search S3 and select the gateway service name e.g. com.amazonaws.eu-north-1.s3
4. Select our custom VPC, and carefully select our private subnet via the route tables.

### 8. Create a new IAM role for S3 Access from EC2
Attaching read only access to our EC2 instances for S3.
1. Navigate to IAM, on the left pane click Roles, and Create role.
2. Choose AWS Service, and Use case will be EC2.
3. Add the policy AmazonS3ReadOnlyAccess
4. Confirm the role name and description, and create the role.
5. Go back to EC2, select an instance, and on the Actions button under security click Modify IAM Role. Attach our new role that allows S3 Read Access.
<img width="1169" alt="image" src="https://github.com/user-attachments/assets/42c9583a-f115-4ff4-be3c-f1b44fdcf49e">

### 9. Test S3 Access from both instances
Logging into our public ec2 from our local machine via ssh and peforming tests. Then ssh to private ec2 from the public instance and confirm permissions also.
1. From my local machine, I navigated to where I had saved public EC2 key.
2. It is required that private keys are not acessible by others, so I ran the appropriate chmod. chmod 600 PE-Public-EC2.pem
3. After logging in as ec2-user, I ran a test with aws S3 ls. You can see our buckets including our projects S3.
[ec2-user@ip-10-1-0-8 ~]$ aws s3 ls
2024-07-29 10:42:36 mynew-firehose-bucket
2024-09-12 10:46:06 p-flowlogs-bucket
2024-09-17 11:09:43 projectendpoint-s3
4. I then ran exit to quit our EC2 instance, and copied our private ec2 instance key into our public ec2 instance /home/ec2-user directory.
5. After logging back into our public instance, I ran ssh into the private instance.
6. I was also successful in running ls here.
[ec2-user@ip-10-1-0-23 ~]$ aws s3 ls
2024-07-29 10:42:36 mynew-firehose-bucket
2024-09-12 10:46:06 p-flowlogs-bucket
2024-09-17 11:09:43 projectendpoint-s3
[ec2-user@ip-10-1-0-23 ~]$ exit
logout
Connection to 10.1.0.23 closed.
7. So the private EC2 instance was accessible without internet access, by jumping from our public instance!

### 10. Next steps.
This was a fundemental project aimed at getting hands on experience with AWS services and deepening my understanding of how they work together. That being said, there are opportunities for future improvements with this project.
1. Introduce NACLs to further enhance security, controlling the subnet traffic rules.
2. Create a custom policy for our EC2 instances accessing S3.
3. Add monitoring and logging through CloudWatch.
4. Automate parts with Terraform.






