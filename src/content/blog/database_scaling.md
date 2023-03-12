---
author: Patrik Kaura
pubDatetime: 2023-02-24T22:46:00Z
title: 📂 4. Database scaling
postSlug: database-scaling
featured: false
draft: false
tags:
  - database
  - system-design
ogImage: ""
description: Ways to scale databases.
---

## Scalability

1. `Vertical scaling` refers to hardware-related changes, such
   as upgrading to a better CPU or larger disks.
2. `Horizontal scaling` involves adding more servers to the system to increase its capacity.
3. `Caching` is a technique that involves storing user data, such as session information, to reduce the time it takes to access frequently used data during TLS connections.
4. `Load balancing` distributes incoming traffic among multiple server instances.
   It is usually set up with two instances for redundancy and can be implemented using hardware or software solutions.
5. `Database replication` involves adding more database instances, such as master-slave, master-master,
   or cluster configurations, to increase capacity, improve performance, and provide fault tolerance.
6. `Database partitioning` is a technique used to divide a large database into smaller, more manageable parts,
   called partitions, to improve performance and scalability.

## Database scaling

### Master-Slave scaling

Master-slave database scaling is a technique used to `increase a database's capacity` and `fault tolerance` by
replicating data from a master database to one or more slave databases. The `master database handles write` requests,
and the `slave databases receive read requests`, which `reduces the load `on the master and improves the system's
overall performance. In the event of a `failure in the master database`, one of the `slave databases can be promoted`
to become the new master, ensuring data availability and system continuity.

![master-slave](/assets/db/db-master-slave.png)

### Master-Master scaling

Master-master database scaling is a technique used to `increase a database's capacity`, `performance`, and
`fault tolerance` by replicating data between two or more master databases. Each master database can handle
`both read and write requests`, which reduces the load on individual databases and increases the system's overall
performance. In the event of a failure in one of the master databases, the `remaining master databases can continue 
to handle read and write requests`, ensuring data availability and system continuity. Master-master database
scaling is suitable for applications with high traffic and strict performance requirements.

![master-master](/assets/db/db-master-master.png)

### Galera cluster (MariaDB)

Galera Cluster is a `synchronous, multi-master database cluster` that uses the Galera Replication
Plugin to manage `replication between nodes`. With Galera Cluster, any node can handle `database reads 
and writes`, and individual nodes can be lost without interrupting operations. Galera Cluster uses certification-based
replication through the extended Write-Set Replication API to ensure strong consistency.
Transactions are replicated asynchronously on each node, making it virtually synchronous.

![galera](/assets/db/db-galera.png)

- Read and write to any node at any time.
- Synchronous replication with no slave lag to avoid data loss if a node crashes.
- Tightly coupled with all nodes holding the same state, preventing diverged data between nodes.
- Multi-threaded slave for better performance and workload handling.
- No need for master-slave failover.
- Hot standby with no downtime for failures or maintenance.
- Automatic node provisioning with no need for manual database backup and copying.
- Transparent to applications with minimal changes if any required.
- No read and write splitting needed.

## Database sharding

Database sharding partitions data in a database into `smaller, distinct chunks or shards`. Each shard could be a table,
schema, or physical database held on a `separate server instance`. Sharding can be done `vertically or horizontally`,
with some data present in all shards and some in a single shard. A `sharding key is used to partition data`,
which could be an `indexed field or indexed compound fields` in every document in the collection. Sharding allows
applications to make fewer queries and improves performance, making scalability issues less of a worry.

![galera](/assets/db/sharding-1.png)

### Sources

- Galera cluster - https://galeracluster.com/
