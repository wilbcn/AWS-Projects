# AWS Log Processing Pipeline

## Introduction
This project demonstrates how to set up a log processing pipeline using AWS services including Amazon Kinesis Data Firehose, Amazon S3, AWS Glue, and Amazon Athena.

## Architecture Diagram
![Architecture Diagram](https://github.com/wilbcn/pngs/blob/main/architecture-diagram.png)

## Setup and Configuration

### 1. Step 1: Create an S3 Bucket
Creating an S3 bucket, our public cloud storage. Steps taken:
1. I created a new general purpose S3 bucket named "mynew-firehose-bucket".
2. ACLs were left as disabled and block all public access was left on by default.
3. Bucket versioning not required for this project.
4. Default encryption "Server-side encryption with Amazon S3 managed keys (SSE-S3)" and Bucket Key enabled.
5. Advanced settings: Object lock was kept off.
6. Create Bucket!

## S3 Overview
![New S3 Bucket](https://github.com/wilbcn/pngs/blob/main/screenshot_of_s3bucket.png)
   

