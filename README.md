### Datastax Certified Apache Cassandra Administrator

* This is some of my notes for passing the certification.
* I found that the provided VM for labworks are not flexible enought so i created a Vagrant to spin some VMs and added some configurations myself.
* Exam is 60 questions and we pass at 70%.
* Read de book and documentation.
* Experience and understanding in distributed Systems helps.

## Foundations

* CQL
    * SSTables are immutable
    * Do not do writes or deletes in place.
    * Last-write-wins conflict resolution
    * Simply chooses the data with the most recent timestamp

* Partitions
    * Groups rows Physically together on disk based on Partition Key.
    * The Partitioner hashes the Partition Key to create Partition Token.

* Primary Keys
    *  Partition Key
        * One or more values (composite)
        * When composite all value must be in WHERE clause in statement.
        
    * Consistent Hashing

* Partition Tokens
    * Computed hash from the Partition Key.
    * Determines the node where the data will go.

* Clustering Columns
    * Non Partition section of Primary Key.
    * Default is Ascending


* Aplication Connectivity
    * Driver for many lamguages:
        * C++
        * C#
        * Java
        * Node.js
        * Python

* Node
    * Can handle 2-4 TB of data.
    * Node that handles the request is the Coordinator.

* Ring
    * Determines how data is distributed in the cluster.

* Peer 2 Peer
    * No one is leader.
    * No one is follower.
    * All nodes are Equal.

* Vnodes
    * Default 128.
    * Help keep cluster balanced in no data skew.
    * Automate Token Ranges.
    * Configured in num_tokens in cassandra.yaml.

* Gossip
    * Internal comuniction between nodes.
    * Purpose is peer discovery and metadata propagation.
    * Runs every second.    
    * Each node gossips with 1-3 peer nodes.

* Snitch
    * Determines which datacenters and racks nodes belong to.
    * They inform Cassandra about the network topology.
    * Requests are routed efficiently and allows Cassandra to distribute replicas.
    * Default is SimpleSnitch.

* Replication
    * Cassandra stores data replicas on multiple nodes to ensure reliability and fault tolerance.
    * Number of replicas and their location are determined by Replication Factor and Replication Strategy
    * Replication Factor: The total number of replicas for a keyspace across a Cassandra cluster.
    * Replication Strategy: Determines the nodes where replicas are placed
        * SimpleStrategy: Single data center only.
        * LocalStrategy: Engine internal purpose only.
        * NetworkTopologyStrategy: How many replicas per DC. 

* Consistency 
    * Many Levels
        * ANY - Stores a hint.
        * ONE, TWO, THREE - Closest to Coordinator.
        * QUORUM - Majority vote.
        * LOCAL_ONE - One in local DC.
        * LOCAL_QUORUM - Majority vote on one DC.
        * EACH_QUORUM - Majority vote in each DC.
        * ALL - All nodes participate.

* Hinted Handoff
    * Optimizes the cluster consistency process and anti-entropy when a replica-owning node is not available.
    * When a node goes down, the remaining nodes will save the writes for this node locally as hints.
    * Default is 3 hours.
    * Enabled by default.

* Read Repair
    * Always occurs when CONSISTENCY level is ALL.
    * Can be triggered manually by nodetool.
    * read_repair_changer - Probability that Cassandra will do read repair with a CONSISTENCY level less than ALL.  

* Node Sync
    * DataStax Enterprise feature.
    * Low overhead backgroup sync.

* Write Path
    * Commit log > MemTable > SSTable.
    * Two places:
        * To disk in append -only CommitLog
        * In-Memory MemTable (sorted by Primary Key)
    * Write Request are Acknowledged as sucessfull onde data is writen in both places.

* Read Path
    * Bloom filter > Key Cache > Partition Summary > Partition Index > SSTable
        * Bloom filter: Space eficient Probabilistic structure to test if an element is a menber of a set.
        * Key Cache: Stores byte offset of most recent accessed records.
        * Partition Summary: In-Memory Data Structure to store offsets for Partition Index.  
        * Partition Index: Index to the SSTable records.

* Compaction
    * Process of combining SSTables, which each may contain data with the same Partition Token into a single SSTable.
    * Process to remove stale data form existing tables.
    * Multiple Strategies: 
        * SizeTierdCompaction
            * The default compaction strategy
            * Triggers when multiple SSTables of similar size are present.
            * Optimized for high rate of Writes.
        * Leveled Compaction 
            * Groups SStables into Levels x10 greater.
            * Optimized for read heavy workloads.
        * TimeWindowCompaction - Optimized for Time Series.
    * When is Compaction Triggered:
        * MemTable flush to disk
        * Executed Manually with nodetool



## Admin

* Configuring Clusters


* Cluster Sizing
    * Data throughput
    * Growth rate
    * Latency

* Cassandra Stress Tool
    * Benchmarking tool to determine schema performance.
        * cassandra-stress user profile=/home/vagrant/labwork/TestProfile.yaml ops\(insert=2,user_by_email=5\) -node 192.168.60.10

* Linux Performance
    * top
    * ps
    * dstat -am

* nodetoll
    * status
    * info
    * ring
    * Flush
    * decomission

* System & Output Logs
    * System
    * output
    * Cassandra
    * Gc logs

* Adding/Removing Nodes
    * nodetool decommission:
         - Live node decommission. 
         - Streams data to other nodes.
    * nodetool remove:
        - Removes node.
        - 
    * nodetool assasinate
        - kills the node
        - Like Kill -9

* Replace a node
    * Replace is better than Remove > Add node.

* Compaction
    * Process of combining SSTables, which each may contain data with the same Partition Token into a single SSTable.
    * Process to remove stale data form existing tables.
    * Multiple Strategies: 
        * SizeTierdCompaction
            * The default compaction strategy
            * Triggers when multiple SSTables of similar size are present.
            * Optimized for high rate of Writes.
        * Leveled Compaction 
            * Groups SStables into Levels x10 greater.
            * Optimized for read heavy workloads.
        * TimeWindowCompaction - Optimized for Time Series.
    * When is Compaction Triggered:
        * MemTable flush to disk
        * Executed Manually with nodetool

* Repair
    * Sync Replicas.
    * Should be done when node is down for some time.
    * Always occurs when CONSISTENCY level is ALL.
    * Can be triggered manually by nodetool.
    * read_repair_changer - Probability that Cassandra will do read repair with a CONSISTENCY level less than ALL.  

* sstablesplit
    * Splits SSTable into multiple tables at a specific size.
    * Maybe needed to make sure compaction happens on very large SSTables.

* Multi Datacenter

* CQL Copy
    * Command to copy data in and out of the cluster.
    * EX: 
        ```
        COPY cycling.cyclist_name (id,firstname) 
        FROM '../cyclist_firstname.csv'
        WITH HEADER = TRUE ;
        ```

        ```
        COPY cycling.cyclist_name (id,firstname) 
        TO '../cyclist_firstname.csv'
        WITH HEADER = TRUE ;
        ```

* ssdtabledump
    * Dump a table for diagnostics.
    * Raw SSTable in (json) format
    * For Diagnostics 

* sstableloader
    * Bulk load data into cluster.
    * Restore snapshots
    * Replicate a node to dev.

* Spark for Data Loading.
    * Using spark can parallelize operations.
    * Can do transoformations and processing prior to loading data.

* DsBulk
    * Datastax Bulk Load tool
    * Several sources.
    * More performant that COPY.

* Backup Fundamentals
    * nodetool snapshot
        * Point in time
        * Snapshot is just a Hardlink
        * Only per node
        * Offsite to S3 or NFS is good.
    * Auto Snapshot - If you do a truncate it does a snapshot first
    * nodetool clearsnapshot

* Backup / Restore
    * Copy files to node
    * ssdtableloader

* JVM Settings
    * cassandra-env.sh.
    * Change java classes and options to the Cassandra runtime.

* Garbage Collection
    * Process of clening obsolete objects from heap.

* Heap Dump
    * Its possible to dump head for diagnostics.

* JVM Tuning
    * Heap Sizes.
    * Gc versions.

* Tuning the Kernel
    * No Swap
    * Avoid NAS, SAN, NFS
    * ulimit, sysctl
    * Nics

* Authentication
    * Ex:
        * nodetool -u cassandra -pw cassandra status
        * cqlsh 192.168.60.10 -u cassandra -p cassandra
    * Replicate system_auth Keyspace
        * ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class':'NetworkTopologyStrategy', 'dc1' : 2};
        * nodetool –u cassandra –pw cassandra repair (on the other nodes to sync    )
    * Change admin password
      
* Authorization
    * Grant permissions to Roles
        * GRANT
            * SELECT, ALTER, AUTHORIZE, CREATE, DROP, MODIFY.
        * REVOKE
* Encryption
    * SSL

* Cloud
    * Considerations for cloud.
