# SD-Discrete-Topic
This doesn't contains topic wise collection of information, it is just the topics details I got confused in while learning system design and got clarification upon exploration.


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

WebSocket connections can stay open for hours — consuming threads, memory, and resources that should be available for DB queries and transactions.

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
Network Protocols
what is Client server Model  
Peer to Peer Model  (and learn about Web sockets)
HTTP vs TCP vs UDP vs FTP vs SMTP(POP, IMAP)
Examples

#2
Latency vs throughput
Availability vs consistency (CAP theorem)
Performance vs scalability
Vertical Scaling
Horizontal Scaling

#3
Api Design
REST
Synchronous and Asynchronous Calls
Blocking and Non Blocking Calls
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


From #9 Onwards
Solve Interview Questions 

**Client Server Protocol**
HTTP, SMTP, FTP and WebSocket

**Peer to Peer**
WebRTC

**Resources**

https://blog.x.com/engineering/en_us/a/2010/announcing-snowflake

https://github.com/sony/sonyflake?tab=readme-ov-

https://arpitbhayani.me/blogs/

https://planetscale.com/blog/the-problem-with-using-a-uuid-primary-key-in-mysql

For distributed systems:

https://www.youtube.com/channel/UC_7WrbZTCODu1o_kfUMq88g/videos

https://www.youtube.com/@lindseykuperwithasharpie/playlists

https://www.youtube.com/@DistributedSystems

https://www.distributedsystemscourse.com/

