## Steffen's Additional Topics

* Edge Computing
The idea of edge computing is to do the computational work as close to the data source/user as possible. This enables processing at greater speed and volume, which leads to less latency. This is especiall important i real time systems.

* Cloud Computing
In general in cloud computing the data sources are connected to a centralized cloud, where the processing of the data happens.

### Communication

### Coordination
A distributed system should try to give its clients the illusion they interact with a single node. Although achieving a perfect illusion is not always possible or desirable, it's clear that some degree of coordination is needed to build a distributed application.

* System Models
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
  * ACID
  * Isolation
  * Atomicity
  * NoSQL
* Asynchronous Transactions
  * Outbox Pattern
  * Sagas
  * Isolation

### Scalability
The simplest and quickest way to increase the application's capacity to handle load is to scale up the machine hosting it. However, the application will eventually hit a hard physical limit that we can't overcome no matter how much money we are willing to throw at it. The alternative to scaling up is to scale out by distributing the application across multiple machines.

* Replication of Data
* Functional Decomposition
  * breaking down an application into separate services, each with its own well-defined responsibility
* Partitioning
  * splitting data into partitions and distributing them among nodes
  * Range Partitioning
  * Hash Partitioning
* Replication
  *  replicating functionality or data across nodes, also known as horizontal scaling
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
