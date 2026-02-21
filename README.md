# SD-Discrete-Topic
This doesn't contains topic wise collection of information, it is just the topics details I got confused in while learning system design and got clarification upon exploration.

<h5>Scalability</h5>
A service is said to be scalable if when we increase the resources in a system, it results in increased performance in a manner proportional to resources added.

Another way to look at performance vs scalability:

If you have a performance problem, your system is slow for a single user.
If you have a scalability problem, your system is fast for a single user but slow under heavy load.

**Scalability rule**
<ul>
  <li> Golden rule for scalability: Every server contains exactly the same codebase and does not store any user-related data, like sessions or profile pictures, on local disc or memory.</li>
  <li>Sessions need to be stored in a centralized data store that is accessible to all your application servers. It can be an external database or an external persistent cache, like Redis.</li>
  <li>An external persistent cache will have better performance than an external database.</li> 
</ul>

How can we make sure that a code change is sent to all your servers without one server still serving old code? <br>
Using _Capistrano_ written in _Ruby_. It requires some learning of Ruby on Rails.

There are two type of caching:

#1 - _Cached Database Queries_

The most common caching pattern involves storing query results in cache using a hashed version of the query as the key. On subsequent runs, the cache is checked first. However, this approach has issues — especially with expiration. When data changes (even a single table cell), it becomes difficult to identify and invalidate all affected cached queries, making cache management complex and error-prone.

#2 - _Cached Objects_

Strongly recommends an **object-based caching pattern**: treat your data like objects (as in code), assemble them in classes (e.g., a `Product` object with prices, texts, images, reviews), and cache the **fully assembled object** or its dataset. This simplifies cache invalidation and boosts performance.

It also enables **asynchronous processing**, where worker servers build and cache objects for the app to consume — reducing direct database access.

Examples of cacheable objects include:

* User sessions
* Rendered blog articles
* Activity streams
* Friend relationships

Biased prefers **Redis** for its advanced features (persistence, data structures) but suggests **Memcached** for simple, high-performance caching due to its scalability.


After we centralized session management and served the same codebase from all our servers, we created an Amazon Machine Image (AMI) from one of them. This AMI acts as a master template for launching new instances. Now, whenever we start a new instance, we simply deploy the latest code, and it’s ready to go.

<H3> Using a WebSocket server inside a database to handle storage or expose DB functionality is not a good idea in most cases. Here’s a detailed breakdown of why it’s discouraged, both conceptually and practically. </H3>

❌ **Why You Shouldn't Use a WebSocket Server in the Database**
1. Breaks Separation of Concerns
Databases are meant to store, index, query, and secure data, not to serve protocols like WebSocket.

Embedding WebSocket logic tightly into the database mixes application and data layers, making the system harder to maintain, debug, and scale.

2. **Complexity and Maintenance**
WebSockets require managing connection states, heartbeats, reconnections, message framing, etc.

Running this logic inside the database engine adds unnecessary complexity to something that should be clean, isolated, and performant.

3. **Performance Penalty**
Database engines are optimized for data access, not handling concurrent long-lived TCP connections.

WebSocket connections can stay open for hours, consuming threads, memory, and resources that should be available for DB queries and transactions.

4. **Security Concerns**
Databases are rarely exposed directly to the public — and for good reason.

Exposing a WebSocket server inside a DB creates additional attack surface: DoS attacks, unauthenticated access, improper sandboxing, etc.

5. **No Tooling/Standards**
There are no standard implementations or mature ecosystems around “WebSocket-database hybrids”.

You lose access to proven tools like connection pooling, proxying, logging, monitoring, and application-layer security (OAuth, rate limiting, etc.).

6. **Scalability Issues**
WebSocket servers need sticky sessions or connection-aware clustering, which are complex to scale.

Databases scale best with short-lived stateless queries rather than long-lived message-passing sessions.


**Distributed system failure matrix**
<img width="958" height="329" alt="image" src="https://github.com/user-attachments/assets/d9483b2c-6726-4bda-a5cd-63d35b8d31a8" />


 
















































**Concept and coding course**

✅ Recommended Architecture Instead
Use three-layer separation:

1. Frontend
Initiates WebSocket or HTTP connection

2. Backend server (Node.js, Spring Boot, Go, etc.)
Handles WebSocket logic

Maintains client sessions, routing, subscriptions

Talks to DB via standard drivers (JDBC, ORM, SQL clients)

3. Database (PostgreSQL, MySQL, MongoDB, etc.)
Only handles queries and data storage

Exposed via optimized drivers or HTTP (if RESTful API exposed)

This ensures loose coupling, better maintainability, observability, and scalability.

🔄 If You Need Real-Time DB Changes
Use patterns like:

PostgreSQL LISTEN/NOTIFY + WebSocket (relay through app layer)

CDC (Change Data Capture) → Kafka → WebSocket

Triggers → Message Queue → WebSocket

Firebase / Supabase (realtime layer built on top of database)

🔚 Summary
You shouldn't use a WebSocket server inside the database because:

It violates separation of concerns

It adds complexity and reduces performance

It's insecure and unscalable

There are better architectural patterns to achieve the same goals

Stick to layering WebSockets over an application backend and let the database do what it’s best at: storing and querying data efficiently.

Let me know your use case — I can recommend a concrete, real-time architecture that fits.

System Design : HLD

#1:
**Network Protocols :** The rules defined between computers in order to communicate.
_Application Layer having two models_

What is Client-Server Model: HTTP, FTP, SMTP, and Web Sockets 

(In Client-server, client gives a request and server gives a response, hence one-way communication, but in <br>

**WebSocet** do have bidirection communication system, although it is not be mistaken as **peer to peer** because what actually happens is two client needs one common server to communicate. For ex, client1 sends a message to client2, but it gets to a server first, and then client2  gets it from there.

**HTTP** has only one connection, hence connection-oriented, which redirects to a web page, and we usually navigate to other pages through the url in it.

**FTP** has 2 connection control connections and a data connection, where the data connection usually gets terminated, where as the control connection remains there. Data connection is not encrypted and hence not secured and that's the reason people avoids its use case.

**SMTP**: Used with IMAP or POP3, used for sending the mail, whereas IMAP is used to access or receive the mail or read the mail. POP3 usually downloads the mail and delete it then hence not getting used widely.

**Peer to Peer Model**  (and learn about Web sockets): WebRTC, Here, all of them can be server and client. It uses UDP because ordering of data is not mandatory, and we can lose some data.

HTTP vs TCP vs UDP vs FTP vs SMTP(POP, IMAP)
Examples

**Transport Layer and Network Layer**
TCP/IP and UDP/IP one have ack, and UDP doesn't, and no connection gets maintained hence no ordering, but it's faster than TCP. UDP breaks data into small packets and sends it without ack.

#2
Desirable property of a distributed system with replicated data
**C stands for consistency**
**A stands for Availability**
**P stands for Partitioning Tolerance**

But not all 3 can be used together; only a combination of two can be used if they can be used

Latency vs throughput
Availability vs consistency (CAP theorem)
Performance vs scalability
Vertical Scaling
Horizontal Scaling

#3
Api Design
REST
Synchronous and Asynchronous Calls
Blocking and Non-Blocking Calls
Rate limiting

#4
Monolithics Vs Microservices
cover all important Microservices pattern like SAGA etc.

#5
Caching
Cache writing policies
Types of Cache REDIS, JUNO ( L1 cache, L2 cache etc).
CDN
Load Balancer
GSLB and other different ways of doing load balancing

#6 
Publish Subscribe pattern
Queues
Pull vs Push
Consistent Hashing
ZooKeeper
Kafka 
Elastic search, SOLR etc.


#7 Cloud Concepts
s3 for amazon, google filestore etc.


#8.1
Database1:   - Relational vs Non Relational
ACID property
Indexing
Data partitioning and Sharding- Vertical vs HorizontalSharding
In Memory Databases

#8.2
Database2:
Replication and Mirroring
Leader Election

<img width="804" height="684" alt="image" src="https://github.com/user-attachments/assets/fa8c59b3-fa08-45f1-a3ff-9d55ad96ca94" />






<h3>DB Scaling</h3><br>

**Vertical DB Scaling (Scale Up)** <br>

_Increase DB server:_

-> More RAM

-> More CPU

-> Faster disk (IOPS)

-> Network

**Horizontal DB Scaling:** <br>

_A) Read Replicas (Most Common)_

Used when:

-- Lots of READ traffic
-- Fewer WRITES

`
                 → Read Replica 1
App → Primary DB → Read Replica 2
                → Read Replica 3 `

`Writes → Primary
Reads  → Replica 1 / Replica 2 / Replica 3`

-- Writes → Primary DB
-- Reads → Replicas

**B) Sharding (True Horizontal Scaling)** <br>

Split data across multiple databases.<br>
Example: User table split by user_id

`Users 1–1M   → DB1
Users 1M–2M  → DB2
Users 2M–3M  → DB3`

**Database Autoscaling (Depends on the Type)** <br>

    Partitioning optimizes queries.
    Read replicas scale reads.
    Sharding scales writes.

 _A) Vertical DB Scaling_

 Mostly manual or semi-automatic. Increase instance size. Often causes brief downtime. Not instant

_B) DB Storage Autoscaling:_ YES — automatic

Most managed DBs support this:
1) Amazon RDS
2) Aurora
3) Cloud SQL

_C) Read Replicas Autoscaling:_  Partially automated

Options:<br>
Manual
You add/remove replicas yourself<br>
Scripted / Policy-based<br>
CloudWatch alarm → Lambda → add replica<br>

**Fully managed (Aurora)**
Aurora can auto-scale read replicas

`Primary DB
   ↓
Aurora auto-adds replicas when read load increases`

_D) Sharding Autoscaling:_  NO — not automatic

We must design sharding logic. Decide shard keys. Move data manually or via custom tooling

NoSQL systems do autoscale extremely well:

Examples: DynamoDB, Cassandra, Bigtable

`Traffic ↑ → DynamoDB auto-adds partitions
Traffic ↓ → DynamoDB scales down`

**What is table partitioning**<br>
Partitioning (Inside One DB)

-- One database instance
-- One DB server

Tables are split into partitions

`Single DB
└── users table
    ├── users_2023
    ├── users_2024
    └── users_2025`

    How the partition looks:
    Schemas
     └── public
         └── Tables
             └── users           ← parent table
                 ├── Partitions
                 │   ├── users_2024_01
                 │   ├── users_2024_02
                 │   └── users_2024_03

      Query like:
      SELECT * FROM users
    WHERE created_at >= '2024-01-01'
      AND created_at <  '2024-02-01';



_Types of partitioning_ <br>

--> Range (date, id)
--> List (region)
--> Hash
--> KEY

**Sharding:**

Sharding (Across Multiple DBs)

Multiple database servers. Each DB holds a subset of data

`App
├── DB1 (users 1–1M)
├── DB2 (users 1M–2M)
└── DB3 (users 2M–3M)`

_Use Partitioning when:_

<li>The table is very large</li>
<li>Queries are slow</li>
<li>You want better performance</li>
<li>Still fits on one DB</li>

_Use Sharding when:_

<li>DB cannot handle data size</li>
<li>Write throughput is too high</li>
<li>Vertical scaling is maxed out</li>

**Global Indexes vs Local Indexes**

_Local index_

<li>Index exists per partition
<li>Faster inserts
<li>Most common

_Global index_

<li>Index spans all partitions
<li>Faster joins sometimes
<li>Slower writes


**Scaling through read replicas**

Read replicas are copies of the primary database used only for READ queries to reduce load on the primary.

                    ┌── Read Replica 1
    App ── Writes ─▶│
                    ├── Read Replica 2
                    │
                    └── Read Replica 3
               ↑
           Primary DB

           Rules:

      Writes → Primary (master)
      
      Reads → Replicas
      
      Replicas are read-only


_Asynchronous Data Replication:_

    Primary writes data
    ↓
    Changes written to WAL / binlog
    ↓
    Replicas pull & apply changes

**How the application reads:**

_A) App-level routing_ 

        UserService
         ├── writeRepository → primary DB
         └── readRepository  → replica DB
         
         POST /users → primary
         GET /users → replica
         
_B) Proxy-based routing_
          
          Tools:
    
    ProxySQL (MySQL)
    
    PgBouncer / HAProxy (Postgres)

    App → DB Proxy → Primary / Replica

    Chances of inconsistency are there due to a lag in data replication across Replicas, and the solution to that is
    -> Read-after-write → primary
    -> Session pinning
    -> Short TTL caching

Breaks when the immediate user reads from primary, but other reads from replica, and then if two users see different truths at the same time, the system is not consistent.

    Write → Primary
            ↓ (later)
            Replica
This can be sorted by using when we allow read only when write has already happened
            
            Writes → Primary
            Reads  → Primary
But then we lose the faster reads, and the primary becomes a bottleneck.

_Synchronous Replication:_

In SQL:

    synchronous_commit = on
    synchronous_standby_names = 'replica1'

Commit waits for replica ACK

      Write → Primary → Replica ACK → Commit success

_Read-After-Write Routing:_

    Writes → Primary
    
    Immediate reads → Primary
    
    Later reads → Replica

    POST /update-profile → Primary
    GET  /profile        → Primary (for 1–2 seconds)
  
Read-after-write pinning guarantees: The same user sees their own write

But it does NOT guarantee: Other users see that write immediately.

    Primary DB
       ↓ (async)
    Read Replica
    
    //Step 1: User A updates profile
    Time T1:
    User A → Write to Primary → SUCCESS
    
    //Step 2: Replication lag
    Primary has new data
    Replica is still old (lag = 500ms–2s)

    //Step 3: User B reads
    Time T1 + 100ms:
    User B → Read from Replica → OLD DATA ❌

    //Step 4: User A reads
    Time T1 + 100ms:
    User A → Read from Primary → NEW DATA ✅

    this is a deliberate trade-off.

    Read replicas choose:
    
    Availability + Performance
    over
    Immediate Global Consistency

    
**Techniques:**

<li> Session pinning
  A) Session pinning

    Store a flag in session / JWT
    
    Example:
    
    {
      "forcePrimaryRead": true,
      "expiresAt": 5s
    }
    
<li> Sticky connections
<li> Request context flag
  
      if (recentWrite) {
          usePrimary();
      } else {
          useReplica();
      }



------------------Need to read the thread more and more-------------------------------------------------
<h3>Event Sourcing</h3>
Event Sourcing is different from CRUD system and is inspired from Event Driven Architecture.

<h4>Event</h4>
<img width="1004" height="474" alt="image" src="https://github.com/user-attachments/assets/19a79bc5-5932-4438-9533-271799390d53" />


Let's say an user is requesting something on our API gateway and API gateway will redirect to the server. To be precise, we will have a reverse proxy for instance nginx and API gateway will redirect he request to the nginx and nginx will forward the request to the server.


<h3>CQRS Design Pattern</h3>
Command Query Responsibility Segregation Principle
<h3>Consistent Hashing</h3>
<h3>Back Of The Envelope Calculation</h3>
<h3>Bloom Filters</h3>









From #9 Onwards
Solve Interview Questions 

**Client Server Protocol**
HTTP, SMTP, FTP and WebSocket

**Peer to Peer**
WebRTC



- IPA Gateway
- Caching
- Queue System
- Broker System
- High Throughput System
- Microservice Architecture
- Container Orcestration
- Cloud Environment
- Application Load Balancer
- Network Load Balancer
- Rate Limiting Strategies
- Scale-out and Scale-in
- Horizontal Scaling
- Verticle Scalling
- VPC
- Auto Scalling Policies
- SQS
- SNS (Simple Notification System)
- Event Driven Architecture
- Fanout Architecture
- READ Replica & Write Replica
- Caching
- CDN
- ClourdFront
- Anycast
1. Back of Envelope
2. Consistent Hashing
3. Quorum
4. Leader and Follower
5. Write-ahead Log
6. Segmented Log
7. High-Water mark
8. Lease
9. Heartbeat
10. Gossip Protocol
11. Phi Accrual Failure Detection
12. Split-brain
13. Fencing
14. Checksum
15. Vector Clocks
16. CAP Theorem
17. PACELEC Theorem
18. Hinted Handoff
19. Read Repair
20. Merkle Trees

1. NPCI- National Payment Corporation of India -  Payment Infrastructure 
2. IMPS- Payment Protocol- payment upto 50k
3. NEFT- Payment Protocol- payment from 50k to 2 lakh
4. RTGS- Payment Protocol - payment more then 2 lakh
5. CPSP- Customer payment service provider 
6. UPI- payment gateway- real-time payment 
7. Partner bank 
8. VPA- virtual payment address

Scalability & Performance
1. Vertical vs Horizontal Scaling
2. Latency & Throughput (P50/P95/P99)
3. Capacity Estimation (QPS, storage, bandwidth)
4. Networking Basics (TCP, HTTP/HTTPS, TLS, UDP, DNS)
5. Load Balancing (L4/L7, RR, least-connections, consistent hashing)
6. CDN & Edge Delivery
7. Sharding & Partitioning (hash, range, geo)
8. Replication (sync/async, leader/follower, read replicas)
9. Consistency Models (strong, eventual, causal)
10. CAP Theorem
11. Redundancy & Failover (multi-AZ/region)
12. Backpressure
13. Rate Limiting & Throttling
14. Queues & Streams (Kafka, RabbitMQ, etc.)
15. Delivery Semantics (at-most/at-least/exactly-once)
↬ Data Storage & Modeling
16. Database Choice (SQL vs NoSQL )
17. Data Modeling & Schema Design
18. Indexing
19. Normalization vs Denormalization
20. Caching Strategies (client, app, DB, CDN)
21. Cache Invalidation
22. Serialization & Schema Evolution (JSON, Avro, Proto)
23. Distributed Transactions (Sagas, 2PC)
24. Event Sourcing & CQRS
25. Consensus & Leader Election (Raft, Paxos)
↬ API & Service Architecture
26. API Design (REST vs RPC/gRPC)
27. API Versioning
28. Service Discovery & Configuration
29. Microservices vs Monolith
30. Deployment Strategies (blue/green, canary, rollbacks)
↬ Reliability, Security & Observability
31. AuthN & AuthZ (OAuth2, JWT, RBAC/ABAC)
32. Circuit Breakers, Timeouts, Retries
33. Observability (logs, metrics, traces, SLI/SLOs, error budgets)
34. Health Checks & Heartbeats
35. Disaster Recovery (backups, RPO/RTO, runbooks)
36. Data Privacy & Retention (encryption, GDPR/CCPA)
Advanced Patterns & Other Essentials
37. Concurrency Control (locks, MVCC, optimistic retries)
38. Multithreading (thread pools, contention, context switching)
39. Idempotency
40. Real-time Delivery (polling, SSE, WebSockets)

**Resources**

https://blog.x.com/engineering/en_us/a/2010/announcing-snowflake

https://github.com/sony/sonyflake?tab=readme-ov-

https://arpitbhayani.me/blogs/

https://web.archive.org/web/20221030091841/http://www.lecloud.net/tagged/scalability/chrono

https://planetscale.com/blog/the-problem-with-using-a-uuid-primary-key-in-mysql

For distributed systems:

https://www.youtube.com/channel/UC_7WrbZTCODu1o_kfUMq88g/videos

https://www.youtube.com/@lindseykuperwithasharpie/playlists

https://www.youtube.com/@DistributedSystems

https://www.distributedsystemscourse.com/

