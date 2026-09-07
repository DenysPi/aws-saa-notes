# RDS

## Overview

- RDS stands for relational Database Service
- It's a managed DB service for DB that use SQL as a query language
- It allows you to create databases in the cloud that are managed by AWS
  - Postgres
  - MySQL
  - MariaDB
  - Oracle
  - Microsoft SQL Server
  - IBM DB2
  - Aurora(AWS)


## Advantage over using RDS versus deploying DB on EC2

- RDS is a managed service:
  - Automated provisioning, OS patching
  - Continuous backups and restore to specific timestamp
  - Monitoring dashboards
  - Read replicas for improved read performance
  - Multi AZ setup for DR(Disaster Recovery)
  - Maintenance windows for ipgrades
  - Scaling capability(vertical and horizontal)
  - Storage backed by EBS
- BUT you can't SSH into your instances

## Storage Auto Scaling

- Helps you increase storage on ypuor RDS DB instance dynamically
- Whzn RDS detecs you are rinng out of free database storage, it scales automatically
- Avoid manually scalling your database storage
- You have to set Maximum Storage Threshold
- Automatically modify storage if:
  - Free storage is less than 10% of allocated
  - Low storge lasts at least 5 mins
  - 6 hours have passed since last modification
- Useful for applications with unpredictable workloads

<img width="242" height="387" alt="image" src="https://github.com/user-attachments/assets/62ae82a6-0937-4e52-87b2-c09e3b1c3d91" />

## Read Replicas

- Up to 15 Read Replicas
- Within AZ, Cross AZ, or Cross Regio,
- Replication is **ASYNC** so reads are consistent
- Replicas can be promoted to their own DB
- Appicationd must update the connection string to leverage read replica

<img width="562" height="381" alt="image" src="https://github.com/user-attachments/assets/fea2d415-52b1-4e7a-8d04-c9280f774be9" />

### Use Cases

- You have a production database that is taking on normal load
- You want to run a reporting application to run some analytics
- You create a Read Replica to run new workflow there
- The prod application iss unaffected
- Read replicad are used for SELECT only

<img width="387" height="387" alt="image" src="https://github.com/user-attachments/assets/92a2420c-e54d-4329-9f04-4874bfe3aa66" />

### Network Costs

- In AWS there’s a network cost when data goes from one AZ to another
- **For RDS Read Replicas within the same region, you don’t pay that fee**

<img width="782" height="266" alt="image" src="https://github.com/user-attachments/assets/cf848e2e-0eef-4ecb-b772-791a76f6703a" />


## Multi AZ(Disaster Recovery)

- **SYNC** replication
- One DNS name - automatic app failover to standby
- Increase avaibility
- Failover in case of loss of AZ, loss of network, instance or storage failure
- No manual intervention in apps
- Not used for scaling

**Note: The Read Replicas can be setup as Multi AZ for Disaster Recovery(DR)**

<img width="397" height="385" alt="image" src="https://github.com/user-attachments/assets/26d0beeb-3533-44ea-aecc-4b10cd43b27d" />

### From Single AZ to Multi AZ

- Zero downtime operation
- Just click on "modify' for the database
- The following happens internally:
  - A snapshot is taken
  - A new DB is restored from the snapshot in a new AZ
  - Syncronization is established between the two databases

<img width="318" height="372" alt="image" src="https://github.com/user-attachments/assets/2388d63a-c4b5-4b0a-b07f-755227d756f4" />

## RDS Custom

- **Managed Oracle and Microsoft SQL Server Database qith OS and database customization**
- RDS: Automates setup, operation and scalling of database in AWS
- Custom: access to the underlying database and OS so you can
  - Configure settings
  - Install patches
  - Enable native features
  - Access the underlying EC2 Instance using SSH or SSM Session Manager
- **De-activate Automation Mode** to perform your customization, better to take a DB snapshot before

