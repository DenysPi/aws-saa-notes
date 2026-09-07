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

