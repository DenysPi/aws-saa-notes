# ElastiCache Overview

## Overview

- ElastiCache is to get managed Redis or MemCached
- Caches are in-memory databases with really high performance, low latency
- Helps reduce load off of databases for read intensive workloads
- Helps make your application staless
- AWS takes care of OS maintenance / patching, optimizations, setup, coniguration, monitoring, failure recovery and backups
- **Using ElastiCache involves heavy application code changes**


## Solution architecture

### DB Cache

- applications queries EladstiCache, if not avaiable, get from RDS and store in ElastiCache
- Helps relieve load in RDS
- Cache mush have an invalidation strategy to make sure only the most current data is used in there

<img width="592" height="443" alt="image" src="https://github.com/user-attachments/assets/80390317-8888-411c-9c6e-38b53e20531f" />

### User Session Store

- User logs into any of the application
- The application writes the session data into ElastiCache
- The user hits another instance of our applicaton
- The instance retrieves the data and the user is already logged in

<img width="612" height="442" alt="image" src="https://github.com/user-attachments/assets/4f2725ae-4353-43ba-bc2c-3c5d1070f301" />


## Redis vs Memcached

**Core question: can you afford to lose the cache?**
Redis = no (it holds data you'd miss) · Memcached = yes (pure cache, just regenerate it)

### Redis
- **Multi-AZ with auto-failover** — standby replica in another AZ takes over automatically
- **Read replicas** — scale reads + serve as failover targets
- **Data durability with AOF persistence** — append-only log of every write, replayed on restart
- **Backup and restore** (snapshots)
- **Supports Sets and Sorted Sets** — leaderboards, ranked data (Memcached: strings only)
- Cluster mode adds sharding - Redis can do replication **and** sharding

### Memcached
- **Multi-node for partitioning of data (sharding)** — more nodes = more total memory
- **No high availability** (no replication)
- **Non-persistent** — node dies, data is gone, app falls back to the DB
- Backup and restore (Serverless only)
- **Multi-threaded architecture** — uses all cores on a large instance

### Sharding vs Replication
- **Sharding** = split data across nodes (node 1: keys A–M, node 2: N–Z) → scales *capacity*
- **Replication** = copy same data to another node → gives *availability*

### When to use which
- **Redis**: sessions, leaderboards, rate-limit counters, job queues — losing it hurts
- **Memcached**: query results, HTML fragments, computed values — losing it just costs latency


## Cache Security

- ElastiCache supports **IAM Aunthefication for Redis**
- IAM policies on ElastiCache are only used for AWS API-level security
- Redis AUTH
  - You can set a "password/token" when you create a Redis cluster
  - This is an extra level of security for your cache
  - Supprors SSL in flight encryption
- Memcached
  - Supports SASL-based authefication

## Use Cases

- Gaming Leaderboards
- **Redis Sorted sets** guarantee both uniqueness and element ordering
- Each time a new element added, it's ranked in real time, then added in correct order

<img width="637" height="193" alt="image" src="https://github.com/user-attachments/assets/264d3e2b-250d-47b9-b40d-3ddd678e1da7" />
