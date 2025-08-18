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
  <li>Sessions need to be stored in a centralized data store which is accessible to all your application servers. It can be an external database or an external persistent cache, like Redis.</li>
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


fter we centralized session management and served the same codebase from all our servers, we created an Amazon Machine Image (AMI) from one of them. This AMI acts as a master template for launching new instances. Now, whenever we start a new instance, we simply deploy the latest code, and it’s ready to go.

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
**Network Protocols :** The rules defined between computers in order to communicate.
_Application Layer having two models_

what is Client server Model :HTTP, FTP, SMTP and Web Sockets 

(In Client server, client gives a request and server gives a response hence one way communication but in **WebSocet** do have bidirection communication system although it is not be mistaken as peer to peer because what actually happens is two client needs one common server to communicate. For ex: client1 sends message to client2 but it gets to a server first and then client2  gets it from there).

**HTTP** have only one connection hence connection oriented which redirects to a web page and we usually navigate to other pages through the url in it.

**FTP** has 2 connection control connection and data connection where data connection usually gets terminated where as the control connection remains there. Data connection is not encrypted and hence not secured and that's the reason people avoids its use case.

SMTP: Used with IMAP or POP3, used for sending the mail where as IMAP is used to access or receive the mail or read the mail. POP3 usually downloads the mail and delete it then hence not getting used widely.


Peer to Peer Model  (and learn about Web sockets): WebRTC, Herre all of them can be server and client. It uses UDP because ordering of data is not mandatory and we can lose some data.

HTTP vs TCP vs UDP vs FTP vs SMTP(POP, IMAP)
Examples

Transport Layer and Network Layer
TCP/IP and UDP/IP one have ack and UDP doesn't, and no connection gets maintained hence no ordering but it's faster than TCP. UDP breaks data into small packets and sends it without ack.

#2
Desirable property of distributed system with replicated data
**C stands for consistency**
**A stands for Availability**
**P stands for Partitioning Tolerance**

But not all 3 can be used together only combination of two if then can be used

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

https://web.archive.org/web/20221030091841/http://www.lecloud.net/tagged/scalability/chrono

https://planetscale.com/blog/the-problem-with-using-a-uuid-primary-key-in-mysql

For distributed systems:

https://www.youtube.com/channel/UC_7WrbZTCODu1o_kfUMq88g/videos

https://www.youtube.com/@lindseykuperwithasharpie/playlists

https://www.youtube.com/@DistributedSystems

https://www.distributedsystemscourse.com/

