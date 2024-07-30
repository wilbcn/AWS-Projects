# AWS Log Processing Pipeline

## Introduction
The goal of this project is to create a data pipeline to ingest, process, and store log data, using AWS services including Amazon Kinesis Data Firehose, Amazon S3, Amazon EC2, AWS Glue, and Amazon Athena. This is a basic project aimed at gaining hands-on experience with a variety of AWS services.

## Architecture Diagram
![Architecture Diagram](https://github.com/wilbcn/pngs/blob/main/architecture-diagram.png)

## Setup and Configuration

### 1. Create an S3 Bucket
Creating an S3 bucket for our public cloud storage. Steps taken:
1. I navigated to S3 on AWS and created a new general purpose S3 bucket named "mynew-firehose-bucket".
2. ACLs were left as disabled and block all public access was left on by default.
3. Bucket versioning was not required for this project.
4. Default encryption "Server-side encryption with Amazon S3 managed keys (SSE-S3)" and Bucket Key enabled.
5. Advanced settings: Object lock was kept off.
6. Create Bucket!

## S3 Overview
![New S3 Bucket](https://github.com/wilbcn/pngs/blob/main/screenshot_of_s3bucket.png)
   
### 2. Set Up Kinesis Data Firehose
Creating a Kinesis firehose delivery stream to send data to our S3 Bucket.
1. I navigated to Kinesis, selected Amazon Data Firehose, and Create firehose stream.
2. Select the source: Direct PUT - Ideal for test data streaming capabilities by manually pushing data into the Firehose stream. We can send data directly from our EC2 instance.
3. Select the destination: Amazon S3. The default stream name is appropriate: PUT-S3-RfICR
4. Transform and convert records is out of scope for this project.
5. Select our newly created S3 Bucket: "mynew-firehose-bucket". Create or update IAM role for this stream. We can showcase this later.
6. The remaining destination settings were left as default.
7. Create firehose stream!

## Firehose stream Overview
![New firehose stream](https://github.com/wilbcn/pngs/blob/main/screenshot_of_firehosestream.png)

### 3. Set up and launch a new EC2 Instance
Creating a new EC2 instance, which we can later install the kinesis agent and generate test data.
1. I navigated to EC2, instances, and clicked launch instances
2. Here I appropriately named our new EC2 instance "Firehose-Test-EC2" and selected Amazon Linux OS.
3. Free Tier AMI and instance type was selected.
4. For this project I created a new key pair and saved this to a new folder for later use. Key type: RSA (.pem)
5. For network settings, I created a new security group, allowing SSH traffic for my IP only. This is good security practice and is ok for this project.
6. Storage options was left at default (free tier) and advanced settings left alone.
7. Launch EC2 Instance! This may take some time after initialising.

## EC2 Instance Overview
![New EC2 Instance](https://github.com/wilbcn/pngs/blob/main/screenshot_of_ec2.png)

### 4. IAM Roles management
Creating a new role for our EC2 instance to give Kinesis and S3 full access.
1. I navigated to S3, roles, and create role.
2. Entity type is AWS Service and use case EC2.
3. Here I attached the following policies:
![IAM Policies](https://github.com/wilbcn/pngs/blob/main/screenshot_of_iampolicies.png)
4. Then I appropriately named our new role "myec2-kinesis-role" and reviewed before hitting create.
5. I then went and attached our new role to our EC2 Instance. EC2 -> Instances -> Our new instance "Firehose-Test-EC2" -> Actions,Security,ModifyIAMrole.
6. From the dropdown menu I selected our new role "myec2-kinesis-role" and clicked update IAM role.

## EC2 Instance Overview with IAM role
![EC2 & IAM](https://github.com/wilbcn/pngs/blob/main/screenshot_of_ec2_withIAM.png)

### 5: Install and Configure Kinesis Agent on our EC2 instance
Now its time to install and configure Kinesis Agent on our newly created EC2 instance.
1. On my MacOS terminal, I navigated to the folder where I saved the downloaded RSA key file.
2. In order to connect to our EC2 instance using our private key, I will ammend the permissions so that only the owner can read it. Private keys are sensitive and should be protected.
![Modifying our key](https://github.com/wilbcn/pngs/blob/main/screenshot_of_chmod.png)
3. With our key modified, I then accessed our EC2 instance via ssh and the ec2-user user, using our instances public ip address found in our instance overview and details tab.
4. I then ran various lines of code, checking for any updates, installing the kinesis agent, and configuring the kinesis agent.json file so that it properly collects and sends data to our EC2 instance. To hide sensitive information, some screenshots are from notepad.
5. ![Installing and configuring Kinesis, editing agent.json](https://github.com/wilbcn/pngs/blob/main/screenshot_install_json.png)
   ![Restarting kinesis after changes](https://github.com/wilbcn/pngs/blob/main/screenshot_restart_kinesis.png)
   ![Sending some test data to S3](https://github.com/wilbcn/pngs/blob/main/screenshot_filesuccess.png)
   ![Checking S3](https://github.com/wilbcn/pngs/blob/main/screenshot_s3_afterfilesend.png)
6. As you can see in the screenshot of our S3 Bucket, we can see our test data
7. Later on once we have configured AWS Glue and Athena, we could potentially experiment with more complex data data like CSV or JSON.

### 6: AWS Glue
Create a glue crawler!
1. Navigate to AWS Glue, Expand on data catalogue, and click Crawlers.
2. Here I clicked create new crawler and named it appropriately "my-log-crawler".
3. Add a data source: Attach our S3 bucket made for this project s3://mynew-firehose-bucket
4. I created another IAM Role for our crawler, so that it can read from the S3 bucket and write to the Glue Data catalogue.
   ![Crawler IAM Role](https://github.com/wilbcn/pngs/blob/main/screenshot_glue_IAM.png)
5. Output configuration: I have created a new database called "logsdatabase" and attached it to the crawler.
6. Create Crawler and run the crawler, checking for successful status.
   ![Crawler Success](https://github.com/wilbcn/pngs/blob/main/screenshot_of_Crawler_success.png)

### 7: AWS Athena
Athena incurs costs, so for this project I will not be running any queries. However I provided a small overview below of how we could use this AWS Service.
As this is our first query, we must specify where the result will end up
 ![New Table](https://github.com/wilbcn/pngs/blob/main/screenshot_athena.png)

Some uses of Athena in relation to cybersecurity would be to analyse files such as application logs, server logs, or access logs, to help us identify unusual patterns or anomalies that could indicate a security breach. For example, looking for repeated failed login attempts or access from unusual IP addresses.









