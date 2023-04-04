## Steffen's Additional Topics



* Edge Computing
The idea of edge computing is to do the computational work as close to the data source/user as possible. This enables processing at greater speed and volume, which leads to less latency. This is especiall important i real time systems.

* Cloud Computing
In general in cloud computing the data sources are connected to a centralized cloud, where the processing of the data happens.

### Communication

### Coordination
A distributed system should try to give its clients the illusion they interact with a single node. Although achieving a perfect illusion is not always possible or desirable, it's clear that some degree of coordination is needed to build a distributed application.

* System Models
* Consensus
  * Paxos for distributed consensus
* Failure Detection
* Time
  * Agreeing on time is not easy
  * Vector Clocks
  * Logical Clocks
  * Vector Clocks
* Quorum
* Leader and Follower (Leader Election)
  * Raft leader election 
* Transactions
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

### Scalability
The simplest and quickest way to increase the application's capacity to handle load is to scale up the machine hosting it. However, the application will eventually hit a hard physical limit that we can't overcome no matter how much money we are willing to throw at it. The alternative to scaling up is to scale out by distributing the application across multiple machines.


#### Vertical Scaling
In vertical scaling the performance of a single machine is increased. This can be done by adding more resources or more powerful processors. At some point the system can't be scaled further due to the limit of resources that can be added. It also gets quit expensiv to add more specialized powerfull resources. 
#### Horizontal Scaling
In horizontal scaling the performance is increased by adding more servers. These servers work together to achieve the request. This increases the complexity as we now have a distributed system.



#### Replication of Data
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

### Data Partitioning
Data Partitioning is the process of splitting a database into smaller chunks. It is used in cases the data does not fit into one node ore to increase the performance of the system.

#### Horizontal Partitioning or Sharding
The idea is to split the table data horizontally based on a partition key. This makes it possible to store smaller tables in each node. Especially needed if the data is not fitting on one machine.

The table is now small and distriubted above multiple machines and each one having the same schema.

The partitioning can be done by range partition, hash partition, list partition, composite partitioning,...

#### Vertical Partitioning
The idea is to reduce the size of a table by splitting by columns. It's also called normalization.


#### Functional Partitioning or Federation
Breaking down an application into separate services, each with its own well-defined responsibility.
Each service stores it's data on a different partition. 
In the end there exist multiple different databases.













######## NOT WORKED ON #######



#### Caching
Caching improves the performance of a system by storing frequently used data in a cache such that a future request can return this data, without any additional overhead.

This leads to an improved latency () and a higher throughput


The main challenge with caching is to keep the data in the cache consistent with the data in the database.

There are multiple Cache Invalidation strategies

Write Through
The data is always written to the cache and then to the database. If it is successfully stored in both a success is returned to the client. Thus, the cache is always consistent with the database.
If there is no correlation between reading the data from the cache shortly after it is written this is not the best strategy as otherwise the cache is polluted with data that is not needed. Thus, there is another approach.

Write Around
In this strategy the data is only written to the cache if the entry already exists. This is needed to have the data consistent. If the data is not in the cache, it is only written to the database. On a read request that has a cache miss the data is loaded into the cache.

Write Back
While the two strategies before gurantee consistency it leads to write latency. Thus, in this strategy the data is only written to the cache until the client get a successfull message. The database is updated asynchronously.These updates can be done periodically. 

The problem with having the latest data only in the cache for some time could lead to data loss due to e.g. power outages. That's because the cache is in volatile memory.

One solution could be to have multiple distributed caches to have the data replicated.

Another Question is about how Cache Eviction Policy
This policy deals with the fact how to add new data to the cache if it is already full. This means which data should be removed from the cache.

Least Recently Used (LRU) : Removes the data that was least recently used
Least Frquently Used (LFU) : Removes the data that was least frequently used
First in First Out (FIFO) : Removes the oldest data in the cache



* Http Caching
* CDN (Content Delivery Networks)
* Network Load Balancing
  * DNS Load Balancing
  * Transport Layer Load Balancing
  * Application Layer Load Balancing 
* Microservices
* Control planes and data planes
* Messaging

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


* Bloom Filters
* Consistent Hashing

* Write-ahead Log
* Segmented Log
* Lease
* Heartbeat
* Gossip Protocol
* Phi Accrual Failure Detection
* Split Brain
* Fencing
* Checksum
* CAP Theorem
* PACELC Theorem
* Hinted Handoff
* Read Repair
* Merkle Trees


# Great Resources
[Free System Design Interview Course](https://www.enjoyalgorithms.com/system-design-courses/)