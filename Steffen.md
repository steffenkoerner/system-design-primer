# Steffen's Additional Topics

**Edge Computing**

The idea of edge computing is to do the computational work as close to the data source/user as possible. This enables processing at greater speed and volume, which leads to less latency. This is especiall important i real time systems.

**Cloud Computing**

In general in cloud computing the data sources are connected to a centralized cloud, where the processing of the data happens.


## Communication

## Coordination
A distributed system should try to give its clients the illusion they interact with a single node. Although achieving a perfect illusion is not always possible or desirable, it's clear that some degree of coordination is needed to build a distributed application.

**System Models**

**Failure Detection**

**Time**

  * Agreeing on time is not easy
  * Vector Clocks
  A vector clock is a (server,version) pair that belongs to a data item.
  * Logical Clocks
  * [Version Vector](https://martinfowler.com/articles/patterns-of-distributed-systems/version-vector.html)
  In distributed systems it is important to detect concurrent updates. This means e.g. in a key-value store if different sever update the same key on it's node. This should be detected as it leads to inconsistency. Thus, this behaviour should be able to be detected. For this, every node maintains a list of counters.
  They can be used for detecting a conflict, but they are not able to resolve it on their own. One solution could be that the latest write wins. This approach is based on time and is quite straight forward. The problem is with synchronising the time between servers. There are some problems with this synchronisation e.g. with protocols like NTP, but still some databases like Voldemort take it into account.
  * [Network Time Protocol (NTP)](https://en.wikipedia.org/wiki/Network_Time_Protocol) is a protocol to synchronise the time between multiple servers
  * Conflict Resolution
    * To be able to detect conflicts and be able to detect them there needs multiple information
    * Thus, a key value store needs to store besides the value also a version vector for conflict detection andd time for resolution

**Read Repair**

One of the most common approaches to fix inconsistency is during a read operation. If there are differences detected and a possible solution found, then the updated version is send out. 

**Quorum**

A quorum is the minimum number of votes that a distributed action has to obtain in order to perform an operation in a distributed system. This is needed
for consistency.

**Leader and Follower (Leader Election) (Consensus)**

  * Raft leader election
  * Paxos

**Heartbeat** 
    
Heartbeat is used to check which servers are online. In a cluster of servers it nees to be known if some servers are not reachable anymore. Thus, servers
send regularly a heartbeat, which is a I am alive signla, to each other. If a heartbeat is not received from a server for some time it is declared
as offline. If each server sends to each other server a heartbeat there will be quite some traffic. Thus, it's often combined with the gossip protocol.

**[Gossip Protocol](https://martinfowler.com/articles/patterns-of-distributed-systems/gossip-dissemination.html)**

The gossip protocol is used for efficient state sharing in distributed systems. Instead that each server sends to each other server a message, which
leads to a flood of messages.
Instead it picks random selection of nodes to pass on information to ensure it reaches all the nodes in the cluster without flooding the network
It's needed as in any large sytem all-to-all multicasting is not a solution as there would be n² messages.



**Transactions**

  * SQL
    * ACID
    * Atomicity - A group of operations are treated as a singlue unit.
    * Consistency -
    * Isolation - Each transaction feels like it is the only one executed
    * Durability - If the data is commited successfully to the database it needs to be persisted.
  * NoSQL
    * CAP theorem(Consistency, Availability, Partition Tolerance)
    * BASE model (Basic Availability, Soft-State, Eventual Consistency)
      * Basically Available ensures that the system is always available
      * Soft State - The system is allowed to change over time
      * Eventual Consistency - That the whole system will at some time in the future be consistent

* Asynchronous Transactions
  * Outbox Pattern
  * Sagas
  * Isolation

## Scalability
The simplest and quickest way to increase the application's capacity to handle load is to scale up the machine hosting it. However, the application will eventually hit a hard physical limit that we can't overcome no matter how much money we are willing to throw at it. The alternative to scaling up is to scale out by distributing the application across multiple machines.


**Vertical Scaling**

In vertical scaling the performance of a single machine is increased. This can be done by adding more resources or more powerful processors. At some point the system can't be scaled further due to the limit of resources that can be added. It also gets quit expensiv to add more specialized powerfull resources. 

**Horizontal Scaling**

In horizontal scaling the performance is increased by adding more servers. These servers work together to achieve the request. This increases the complexity as we now have a distributed system.



## Replication of Data
Replication means to keep several copies of the same data in different places.This can be done for backup, fault tolerance, and improved overall network accessibility
  * Prevents loss of data. If a node fails, the data is still available in another node. 
  * Increases availability if a node is down
  * Read Scalability: Helps in load balancing as the queries can be split between multiple copies
  * Reduced Latency: Data could be hold closer to the customer
* Replication Methods
  * Single Leader Replication (Master Slave Replication)
  One server accepts the writes from the client and the replicas pull the data from this server (leader). This means that all writes need to go to the leader, which writes the data first and then sends an update to the follower. The update is send via the replication log.
  The read requests can go to the leader or follower. Often it only goes to the follower to keep the leader ready for writes.

  The leader can be manually specified, but generally it is selected via a consensus algorithm.
  * Multi Leader Architecture (Master Master Replication)
  Multiple Servers accept read and writes.
  * No Leader Architecture
  Each server accepts writes.
  *  replicating functionality or data across nodes, also known as horizontal scaling
  * Primary-Replica Replication
  * multi-primary replication strategy

Synchronous Replication
If a write goes to the leader it sends it to the follower. It is a synchronous replication if the leader only returns success after all the followers returned success.
This ensures strong consistency ? Is this true. Isn't there still a difference during small time periods. 

Asynchronous Replication
In this case the leader does not wait for the follower to report a success to him before he returns a success to the client. This is is very quick and thus the system is available, but not consistent. It is eventual consistent as some time passes until all are converged to the same status.

Semi Synchronous Replication
Normally the propagation of the change to the follower is fast. But if a follower is not reachable due to network issues, rebooting it can take very llong.

In contrast the asynchronous replication can lead to data loss if the master fails. A good solution would be to semi synchronous replication.

In this mode one follower are set to synchronous, while the others are set to asynchronous. If there are problems with the synchronous follower another can be promoted to synchronous follower. This promotion can be done via consensus.

This ensures that at least two nodes have the latest data.

## Data Partitioning
Data Partitioning is the process of splitting a database into smaller chunks. It is used in cases the data does not fit into one node ore to increase the performance of the system.

### Horizontal Partitioning or Sharding
The idea is to split the table data horizontally based on a partition key. This makes it possible to store smaller tables in each node. Especially needed if the data is not fitting on one machine.

The table is now small and distriubted above multiple machines and each one having the same schema.

The partitioning can be done by range partition, hash partition, list partition, composite partitioning,...

### Vertical Partitioning
The idea is to reduce the size of a table by splitting by columns. It's also called normalization.


### Functional Partitioning or Federation
Breaking down an application into separate services, each with its own well-defined responsibility.
Each service stores it's data on a different partition. 
In the end there exist multiple different databases.





## Caching
Caching improves the performance of a system by storing frequently used data in a cache such that a future request can return this data, without any additional overhead.

This leads to an improved latency () and a higher throughput


The main challenge with caching is to keep the data in the cache consistent with the data in the database.

There are multiple Cache Invalidation strategies

**Write Through**

The data is always written to the cache and then to the database. If it is successfully stored in both a success is returned to the client. Thus, the cache is always consistent with the database.
If there is no correlation between reading the data from the cache shortly after it is written this is not the best strategy as otherwise the cache is polluted with data that is not needed. Thus, there is another approach.

**Write Around**

In this strategy the data is only written to the cache if the entry already exists. This is needed to have the data consistent. If the data is not in the cache, it is only written to the database. On a read request that has a cache miss the data is loaded into the cache.

**Write Back**

While the two strategies before gurantee consistency it leads to write latency. Thus, in this strategy the data is only written to the cache until the client get a successfull message. The database is updated asynchronously.These updates can be done periodically. 

The problem with having the latest data only in the cache for some time could lead to data loss due to e.g. power outages. That's because the cache is in volatile memory.

One solution could be to have multiple distributed caches to have the data replicated.

Another Question is about how Cache Eviction Policy
This policy deals with the fact how to add new data to the cache if it is already full. This means which data should be removed from the cache.

**Least Recently Used (LRU) :** Removes the data that was least recently used
**Least Frquently Used (LFU) :** Removes the data that was least frequently used
**First in First Out (FIFO) :** Removes the oldest data in the cache


######## NOT WORKED ON #######

* Messaging

* Http Caching
* CDN (Content Delivery Networks)
* Network Load Balancing
  * DNS Load Balancing
  * Transport Layer Load Balancing
  * Application Layer Load Balancing 
* Microservices
* Control planes and data planes


### Resiliency
Every part has a probability of failing, the more moving parts there are, the higher the chance that any of them will fail. Eventually, anything that can go wrong will go wrong; power outages, hardware faults, software crashes, memory leaks — you name it.

To guarantee just two nines of availability, an application can only be unavailable for up to 15 minutes a day. That's very little time to take any manual action when something goes wrong. And if we strive for three nines, we only have 43 minutes per month available. Clearly, the more nines we want, the faster our systems need to detect, react to, and repair faults as they occur.

* Common Failure Causes
  * Hardware faults
  * Incorrect error handling
  * configuration changes
  * single point failure
  * network faults
  * resource leaks
  * load pressure
  * cascading failure
  * managing risk 
* Redundancy
* Fault Isolation
  * Shuffle Sharding
  * Cellular Architectures
* Downstream Resiliency
  * timeouts
  * retries
  * circuit breaker
* Upstream Resiliency
 * load shedding
 * load leveling
 * rate-limiting
 * constant work


### Maintainability
* Testing distributed systems
* CI/CD
* Monitoring
  * SLI, SLO, Health, Dashboards
* Observability
* Manageability
  * Change a system without code modification

* Memmap
  A memmap is an in-memory balanced tree data structure
* Sorted String Table (SSTable) Really good explanation in the Design Data Intensive Applications Book of Martin Kleppmann
  SSTables is a persistent file format that is used by many databases like Apache Cassandra.
  They take data that is stored in memory in memtables, order it and write it to disk for fast data access.
  The SSTables are immutable. This means they are never modified as operatinos like insert or delete would require many I/O to rewrite the file.
  For faster access to a SSTabe an index file that contains key/offset mapping can be introduced.

  SStable Compaction
  The data is flushed from memmap to the SSTable. Thus, the amount of SSTables is growing and the disk is getting full. If data needs to be searched the search starts in memap and then with the latest SSTable and so forth. If a key e.g. is not in the databse, then all SSTables need to be searched that takes very long. Also the same keys are in multiple SSTables if they are written multiple times. These keys can be put together. This is what SSTable Compaction does. It merges multiple SSTables together and just take the latest keys.

* Log Structured Merge Trees (LSMT)
  Storage engines that use merging and compacting of sorted files (SSTablles) are often called LSM storage engines.

* B Trees

* Bloom Filters
 They are a probabilistic data structure that can detect if data is in e.g. a hashmap if not all keys can be stored in the cache.
 It uses multiple hashfunctions to map a key onto an index in an array. It then sets this entry to existing. Each key is hashed by multiple
 hashfunctions. 
 A query to the bloom filter checks the indices of the where the key is hashed to. If all of them are set to exist the key is probable inside. It's no gurantee as other keys can map to these indices as well. But if it states it is not in the map, then it is guranteed to not be there.

 THe NOSQL database [Cassandra is using bloomfilters](https://cassandra.apache.org/doc/latest/cassandra/operating/bloom_filters.html) per SStable to check if a key is in the SStable.


* Consistent Hashing
* Write-ahead Log
* Segmented Log
* Lease
* Last-Write-Wins
* Phi Accrual Failure Detection
* Split Brain
* Fencing
* Checksum
* CAP Theorem
* PACELC Theorem
* Hinted Handoff
* Read Repair

**Merkle Trees**

A merkle tree also called hash tree is a secure and efficient way of validating large data structures. 
Is a tree where each leaf is labelled with the hash of a data block. If a node is not a leafe it's labelled with the hash of it's children.
It's e.g. used in NOSQL databases to check if replicas are in sync. This means inconsistency checks and minimizing the amount of data that need to be send to bring these two replicas into a synced state.

**Service **Disocvery**
You don't want to have hard coded ip adresses as services come an go. The service disovery helps us to know where each instance is located. It acts like a registry in which all the adresses of the instances are tracked.




# Databases
Short summary [here](https://www.baeldung.com/cs/distributed-systems-guide)
## Dynamo
Cassandra is an open-source, distributed key-value system that adopts a partitioned wide column storage model. It features full multi-master data replication providing high availability with low latency. It’s linearly scalable with no single point of failure.

## Cassandra
Wide Column NoSQL Database

## MongoDB
MongoDB is an open-source, general-purpose, document-based, distributed database that stores data as a collection of documents.

## Kafka
Distributed Messaging System

## Redis
Redis is an open-source data structure store that we can use as a database, cache, or even a message broker. It supports different kinds of data structures like strings, lists, maps, to name a few. It’s primarily an in-memory key-value store with optional durability.

## GFS
Distributed File System Storage

# Great Resources

One of the best sites for learning about linux, computer science, kotlin,...
[Baeldung](https://www.baeldung.com/linux/linux-scripting-series)

Okaish
[Free System Design Interview Course](https://www.enjoyalgorithms.com/system-design-courses/)

[Patterns of Distributed Systems] (https://martinfowler.com/articles/patterns-of-distributed-systems/)




## Microservice Patterns
- API Gateway Pattern
- Strangler Fig
- Backend for Frontends (BFF)
- Service Discovery Pattern
- Circuit Breaker
- Bulkhead
- Retry
- Sidecar
- Saga Pattern
- Event Driven Architecture
- Command Query Responsibilit Segregation
- Configuration Externalization Pattern
- 



# Requirements from different Job Advertisments

## Backend
- Python -> Django, Flask
- Java
- TypeScript, Node
- Rust
- Testingframework (NSubstitute)
- RESTful APIs
- GraphQL
- Docker
- Kubernetes
- Cluster e.g. AWS, Azure, Google Cloud
- CI/CD
- Microservices

## SRE
- Monitoring
- Automation
- Troubleshooting skills
- Disaster Recovery


