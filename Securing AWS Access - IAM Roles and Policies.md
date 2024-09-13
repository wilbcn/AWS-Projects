# AWS Project - Monitoring traffic with VPC Flowlogs

## Overview

This project aims to implement secure access control for two different departments (Operations and HR), using AWS IAM roles and policies. In this project we will demonstrate how to follow best security practices like the Principle of Least Privilege (PoLP), by assigning only the minimal and necessary permissions to users based on their department and job function. 

Each department will have its own EC2 instance, and users will only have access to their retrospective resources without any permissions to access or modify resources outside of their department. This project was complete to gain hands-on experience using AWS services, with an emphasis on security by ensuring that resources are properly segrated and managed, helping to prevent unauthorised access to sensitive data. 

# Project Scope
1. Create a VPC with two EC2 instances, one for each department: Operations, HR.
2. Configure users in AWS Identity Centre and roles for each department.
3. Ensure that users have the appropriate access and permissions for a secure environment.
4. Implement role-based access control policies for our EC2 instances.
5. Test and validate that user control access. 

## Steps to Implement

### 1. Create secure EC2 instances for each department.
Steps taken with relevant screen shots.
1. In this section I setup two new EC2 instances, using the default VPC.
2. These instances were kept at the free tier, with ssh traffic allowed from my IP address only.
3. I also assigned appropriate tags that will assist in creating policies.
4. <img width="1604" alt="image" src="https://github.com/user-attachments/assets/41861a4c-b40e-4a48-b166-6454dd22d151">

### 2. Create Users for HR and Operations using AWS Identity Center.
Steps taken with relevant screen shots.
1. Navigated to the IAM dashboard, then onto IAM Identity Center, and Users.
2. I made a new user called HR-Manager, and for password I selected generate a one-time password.
3. I filled out this users details using my own personal information as this is for the project.
4. I then added this user to a new group called HR Staff, and noted down the login information.
5. I repeated this step for our Operations-Lead user, adding them to a new group under Operations Team.
6. <img width="1548" alt="image" src="https://github.com/user-attachments/assets/cfbcfa14-2f23-4655-8b47-399d6b53e4c1">
7. I received an email for verification, and after logging in, was able to proceed to setup MFA.
8. <img width="939" alt="image" src="https://github.com/user-attachments/assets/ed92fd8b-d763-4432-804b-312acaf7c627">
9. I also updated my account to a new, strong and secure password.
10. <img width="1400" alt="image" src="https://github.com/user-attachments/assets/ce497ce5-0adf-4b14-bd86-3d386ec5c1b2">]
11. Now I did the same with our Operations User.
12. <img width="1536" alt="image" src="https://github.com/user-attachments/assets/53557a32-f216-486f-a695-05cb9abd6dd9">















