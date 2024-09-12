# AWS Project - Monitoring traffic with VPC Flowlogs

## Overview

This project aims to implement secure access control for two different departments (Engineering and HR), using AWS IAM roles and policies. In this project we will demonstrate how to follow best security practices like the Principle of Least Privilege (PoLP), by assigning only the minimal and necessary permissions to users based on their department and job function. 

Each department will have its own EC2 instance, and users will only have access to their retrospective resources without any permissions to access or modify resources outside of their department. This project was complete to gain hands-on experience using AWS services, with an emphasis on security by ensuring that resources are properly segrated and managed, helping to prevent unauthorised access to sensitive data. 

# Project Scope
1. Create a VPC with two EC2 instances, one for each department: Engineering, HR.
2. Configure IAM users and roles for each department.
3. Ensure that users have the appropriate access and permissions for a secure environment.
4. Implement role-based access control policies for our EC2 instances.
5. Test and validate that user control access. 

## Steps to Implement

### 1. Create secure EC2 instances for each department.
Steps taken with relevant screen shots.
1. In this section I setup two new EC2 instances, using the detault VPC.
2. 
