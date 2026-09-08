# Aurora

## Overview

- Aurora is a proprietary technology from AWS(not open sourced)
- Postgres and MySQL are both supported as AuroraDB(drivers will work as if Aurora was a Postgres or MySQL database)
- Aurora is "AWS cloud optimized" and claims 5x performance improvement over MySQL on RDS, over 3x the performance of Postgres on RDS
- Aurora storage automatically grows in increment of 10GB, up tp 256TB
- Aurora can have up to 15 replicas and the replication process is faster than MySQL
- Failover in Aurora is instantaneous. It's HA native
- Aurora costs more than normal RDS(20% more)

## HA and Read Scaling

- 6 copies of your data across 3 AZ:
  - 4 copies out 6 needed for writes
  - 3 copies out 6 need for reads
  - self healing with peer to peer peplications
  - Storage is striped across 100s of volumes
- One Aurora INstance takes writes
- Automated failover for master in less than 30 seconds
- Master + up to 15 Aurora Read Replicas serve reads
- Support for Cross REgional Replication

<img width="352" height="290" alt="image" src="https://github.com/user-attachments/assets/c6b03279-756a-42b3-b730-bc2717a16c4f" />

<img width="821" height="446" alt="image" src="https://github.com/user-attachments/assets/ccc1134f-9d18-49de-afff-446bb4e96a92" />

## Features of Aurora

- Automatic fail over
- Backup and recovery
- Isolation and security
- Industry compliance
- Push button scaling
- Automated Patcing with Zero Downtime
- Advanced Monitoring
- ROutine Maintenance
- Backtrack: restore data at any point of time without using backups

## Advanced Concepts

### Aurora Replicas - Auto Scaling

<img width="792" height="438" alt="image" src="https://github.com/user-attachments/assets/b18e55cf-7ea2-428e-8d82-16c683bdb3e8" />

### Custom Endpoints

- Define a subset of Aurora Instances as a Custom Endpoint
- Example: Run analytical queries on specific replicas
- The Reader Endpoint is generally not used after defining Custom Endpoints

<img width="908" height="348" alt="image" src="https://github.com/user-attachments/assets/5ae626e2-a840-46bb-a33d-f2aba631a67e" />

### Aurora Serverless

- Automated database instantiation and auto scaling based on actual usage
- Good for infrequent intermittent or unpredictavle workloads
- No capacity planning needed
- Pay per second, can be more cost efective

<img width="511" height="447" alt="image" src="https://github.com/user-attachments/assets/8754547a-de0b-4768-b08d-4f119aca2969" />

### Global Aurora

- **Aurora Cross Region Read Replicas**
  - Useful for disaster recovery
  - Simple to put in place
- **Aurora Global Database (recommended)**
  - 1 Primary Region(read / write)
  - Up to 10 secondary(read-only) regions, replication lag is less than 1 second
  - Up to 16 Read Replicas per secondary region
  - Helps for decreasing latency
  - Promoting another region(for disaster recovery) has an RTO of < 1 minute
  - **Typical cross region replication takes less than 1 second**

<img width="273" height="488" alt="image" src="https://github.com/user-attachments/assets/fdf7421d-685a-4ef8-86ca-779eb1ef3be5" />

### Aurora Machine Learning

- Enables you to add ML-based predictions to your applications via SQL
- Simplee, optimized and secure integration between Aurora and AWS ML services
- Supported services:
  - Amazon SageMaker(use with any ML model)
  - Amazon Comprehender(for sentiment analysis)
- You don't need to have ML experience
- Use cases: fraud detection, ads targeting, sentiment analysis, product recommendations

<img width="372" height="427" alt="image" src="https://github.com/user-attachments/assets/b1e977a5-3a41-43d3-86a0-7c544611d602" />

### Babelfish

- Allows Aurora PostgeSQL to understand commands targeted for MS SQL Server
- Therefore Microsoft SQL Server based applications can work on Aurora PostgreSQL
- Requires no to little code changes
- The same applications can be used after a migration of your database

<img width="438" height="416" alt="image" src="https://github.com/user-attachments/assets/37a23be9-a3cd-4c3b-b1f3-d20c9e2b2861" />

## Backup and Monitoring  

### RDS Backups

- Automated backups:
  - Daily full backup of the database
  - Transaction logs are backed up by RDS every 5 mins
  - ability to restore to any point in time
  - 1 to 35 days of retention
- Manual DB Snapshots
  - Manually triggered by the user
  - Retention of backup for as long as you want

**Trick: in a stopped RDS DB, you will still pay for storage. If you plan on stopping it for a long time, you should snapsot & restore instead**

### Aurora Backups

- Automated backups
  - 1 to 35 days(cannot be disabled)
  - point-in-time recovery in that timeframe
- Manual DB Snapshots
  - Manually triggered by the user
  - Retention of backup for as long as you want
 
### RDS & Aurora Restore options

- **Restoring a RDS / Aurora backup or a snapshot** creates a nex database
- **Restoring MYSQL RDS database from S3**
  - Create a backup of your on premises database
  - Storeit on Amazon S3
  - Restore the backup file onto a new RDS instance running MySQL
- **Restoring MySQL AUrora cluster from S3**
  - Create a backup of your on-premises database using Percona XtraBackup
  - Store the backup file on Amazon S3
  - Restore the backup file onto a new Aurora cluster running MySQL


### Database Cloning

- Create a new Aurora DB Cluster from an existing one
- Faster than snapshot & restore
- Uses **copy-on-write** protocol
  - Initially, the new DB cluster uses the same datavolume as the original DB cluster (fast and efficient, no copying is needed)
  - When updates are made to the new DB cluster data, then additional storage is allocated and data is copied to be separated
- Very fast & cost-effective
- **Useful to create a “staging” database from a “production” database without impacting the production database**


## RDS & Aurora Security

- At-rest encryption:
  - Database master & replicas encryption using AWS KMS – must be defined as launch time
  - If the master is not encrypted, the read replicas cannot be encrypted
  - To encrypt an un-encrypted database, go through a DB snapshot & restore as encrypted
- In-flight encryption: TLS-ready by default, use the AWS TLS root certificates client-side
- IAM Authentication: IAM roles to connect to your database (instead of username/pw)
- Security Groups: Control Network access to your RDS / Aurora DB
- No SSH available except on RDS Custom
- Audit Logs can be enabled and sent to CloudWatch Logs for longer retention
