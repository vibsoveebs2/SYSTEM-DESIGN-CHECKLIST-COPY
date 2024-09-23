# SYSTEM-DESIGN-CHECKLIST-COPY
 
# SYSTEM DESIGN CHECKLIST
 
### Comprehensive System Design Prompt Checklist (with API Design)

**1. Functional Requirements**  
- What are the primary features of the system?  
- What are the specific user interactions?

**2. Non-Functional Requirements**  
- What scale should the system handle?  
- What are the key performance metrics?  
- What are the system’s reliability, privacy, and security needs?

Here’s an **empty checklist** that can be used for any system design problem, including the necessary sections like back-of-the-envelope estimations, database design, and how the system works:

---

### **Empty Checklist for System Design**

---

### **Core Requirement**

The core requirement of the system is to handle and fulfill specific user functionality (e.g., real-time tracking, data storage, or notifications). The service must efficiently manage the operations and ensure proper system behavior under load.

#### **Out-of-the-Box Solutions**:
1. **Core Functionality**: Define how the system will achieve its primary goal (e.g., online status, data processing, etc.). Consider alternatives or optimizations that aren’t part of traditional approaches but help fulfill the requirements better.
   
2. **Data Processing/Storage**: Define any regular operations (e.g., cleanup, batch processes) that are required to maintain the health of the system, such as regular data purging or asynchronous operations to ensure performance is maintained.

---

### **Data Sharding & Memory Storage**

The system will often need to be distributed across multiple nodes or servers. Here's how data sharding and memory management should work:

1. **Sharding Logic**:  
   - Each user or entity is assigned to a specific shard based on a defined shard key (e.g., `user_id`). 
   - Use a formula (e.g., `entity_id % buckets = shard_id`) to decide where the data for each entity should be stored. This ensures even load distribution.
   
2. **Server Buckets**:
   - Ensure each shard contains a list of servers for redundancy. These servers can share responsibilities to maintain availability in case of failure.

3. **In-Memory Structures**:
   - **Map**: Track important data (e.g., `user_id -> status` or `timestamp`) in an in-memory map for fast access.
   - **Bloom Filters**: Use bloom filters for efficient existence checks to minimize costly database lookups.
   - **Subscriptions**: Maintain subscriptions or connections in memory (e.g., `entity_id -> websocket connections`).

---

### **How the System Works**

1. **User/Entity Update**:
   - Describe the logic for handling incoming updates, such as activity updates or data changes, and how they are processed (e.g., calculating the shard for the entity and updating the necessary in-memory structures).

2. **Storage Redundancy**:
   - Define how the system maintains redundancy for high availability (e.g., replicating data across multiple servers or shards).
   
3. **Cleanup or Maintenance Process**:
   - Define any cleanup jobs that run periodically (e.g., removing inactive data or purging old subscriptions). Also, explain how data is written to the database asynchronously to ensure performance.
   
4. **Subscription Management**:
   - Explain how subscriptions are managed and updated. This can include appending connections to subscription lists and sending updates to subscribers when necessary.

---

### **Walkthrough of Core Requirement**

1. **Entity/Request Flow**:
   - Walk through the flow of the core feature (e.g., how an entity is processed from when it sends a request until the data is stored or a notification is sent).

2. **Event Notification**:
   - Explain how the system notifies relevant subscribers or systems when an event happens, such as changes to an entity’s status or activity.
   
3. **Handling Inactivity**:
   - Define how the system handles inactivity or similar events, including any background processes that mark entities as inactive and handle subscriptions accordingly.

4. **Subscription Notification Flow**:
   - Detail how subscribers are notified when an event (e.g., status change, new data) occurs for the entity they are subscribed to.

---

### **Optimization of Read and Write Operations**

#### **Read Optimization**:
- **In-Memory Caching**: Define how frequently accessed data will be stored in memory to avoid unnecessary database lookups.
- **Bloom Filters**: Describe how bloom filters can reduce the overhead of checking for existence in databases or large in-memory structures.

#### **Write Optimization**:
- **Asynchronous Writes**: Explain how non-critical data is written to the database asynchronously to improve system performance and reduce latency.
- **Batch Processing**: If applicable, define how write operations are batched (e.g., during cleanup) to optimize performance and avoid frequent small writes.

---

### **Back-of-the-Envelope Estimation**

1. **Per Entity Resource Requirements**:
   - Estimate how much memory/storage is required for each entity (e.g., `user_id -> data`). Break down memory or storage needs per entity or connection.

2. **Scaling to Total Entities**:
   - Provide estimates for scaling the system to handle large numbers of entities (e.g., millions of users) and calculate the corresponding memory and server needs.

3. **Total System Resource Needs**:
   - Based on the per-entity calculation, extrapolate to the total memory, storage, and computational resources required. Include considerations for redundancy and failover strategies.


---

### **3. API Request Throughput**:
- **1 million users/day**, 5 API calls each.
- **138 requests/second** at peak.

**Impact**: **Rate Limiting** at **API Gateway** and auto-scaling **Application Servers**.

---

### **4. Consistency & Availability**:
- Eventual consistency for metadata, **multi-region replication** for critical data.

**Impact**: Emphasize **Database** sharding and replication in the design.


**5. Basic API Design**  
- **Endpoints**: List the core API endpoints and their functionality.  
- **Request/Response**: Define the structure of the requests and responses.  
- **Authentication**: How will user authentication and authorization be handled?  
- **Versioning**: Will the API be versioned?  
- **Rate Limiting**: How will the API handle rate limiting to protect against abuse?
POST /status/update:
Functionality: Updates the presence status of a user.
Example function: updateStatus(userId, status)
This function will be responsible for updating a user's online/offline status based on the provided activity data.
GET /status/{userId}:
Functionality: Retrieves the current presence status for a specific user.
Example function: getStatus(userId)
This function fetches the status of a user from the in-memory store or cache.
DELETE /status/{userId}:
Functionality: Removes or cleans up the presence status of a user if they have been inactive for an extended period.
Example function: deleteStatus(userId)
This function handles the cleanup mechanism by purging old records from the system.
POST /status/subscribe:
Functionality: Allows a client to subscribe to status updates for a list of users.
Example function: subscribeToStatus(userId, subscriberId)
This function registers subscribers who want updates on a particular user's presence status.


**6. Database Design**

- **Database Types**:

  - **Relational Database (SQL)**:
    - **Usage**: For structured data like user accounts and video metadata.
    - **Examples**: PostgreSQL, MySQL.
  
  - **NoSQL Database**:
    - **Usage**: For scalable storage of comments, likes, and activity logs.
    - **Examples**: MongoDB, Cassandra.
  
  - **In-Memory Database**:
    - **Usage**: Use Redis for caching and session management.

- **Sharding and Partitioning**:

  - **Horizontal Sharding**:

    - **Shard Key Selection**:

      - **Choice**: We will use **`user_id`** as the shard key to distribute data across nodes.

      - **Reasoning**:

        1. **Access Patterns Alignment**:
           - **User-Centric Operations**: Many operations involve user-specific data—user profiles, watch history, personalized recommendations.
           - **Data Locality**: Storing user-related data together reduces cross-shard queries for user-centric actions.

        2. **Uniform Data Distribution**:
           - **High Cardinality of `user_id`**: A large and growing number of users ensures even data distribution across shards.
           - **Balanced Load**: Read and write operations are spread out, preventing any single shard from becoming a hotspot.

2a)  **How the System Interacts with the Database**:
   - Walk through how the system performs reads and writes, optimizations for indexing, and how it ensures high performance during large-scale operations.


        3. **Scalability**:
           - **Horizontal Scaling**: As the user base grows, new shards can be added to handle increased load.
           - **Dynamic Scaling**: Consistent hashing minimizes data movement when scaling out.

        4. **Simplified Maintenance**:
           - **Data Management**: User data can be managed, backed up, and restored independently.
           - **Isolation**: Issues affecting one user's data do not impact others.

        5. **Challenges and Mitigations**:
           - **Cross-Shard Queries for Video Data**:
             - **Challenge**: Videos uploaded by different users reside on different shards, which may complicate operations like generating a global feed.
             - **Mitigation**:
               - **Denormalization**: Store copies of frequently accessed video metadata in a centralized index.
               - **Secondary Indexes**: Use a search service like Elasticsearch to index video metadata across shards.

    - **Data Structures for Sharding**:

      - **Consistent Hashing**:
        - **Usage**: Evenly distribute user data across shards and handle node additions/removals gracefully.
        - **Benefit**: Minimizes data redistribution when scaling.

      - **B-Trees**:
        - **Usage**: Implement B-Tree indexes on `user_id` for efficient query performance.
        - **Benefit**: Fast data retrieval and range queries.

  - **Vertical Sharding (Partitioning)**:

    - **Definition**: Dividing the database into multiple pieces based on functionality or data domains.

    - **Implementation**:

      - **User Data Database**:
        - Stores user profiles, credentials, preferences.
        - Optimized for read/write operations related to user management.

      - **Video Metadata Database**:
        - Contains video titles, descriptions, tags, and metadata.
        - Optimized for quick read access and search functionality.

      - **Interaction Data Database**:
        - Stores comments, likes, view histories.
        - Requires high write throughput and scalable storage.

      - **Analytics and Logging Database**:
        - Collects logs, metrics, and analytics data.
        - Designed for batch processing and analytical queries.

    - **Benefits**:

      - **Performance Optimization**: Each partition can be optimized for its specific workload.
      - **Independent Scaling**: Scale each partition based on its load and storage needs.
      - **Enhanced Security**: Sensitive data can be isolated and secured separately.

Here’s the updated checklist with **examples** for the types of data structures mentioned, along with explanations of their usage, how they solve specific problems, and where they fit into a **plaintext architecture diagram**.

---

### 7 **Data Structures for Storage and Indexing**:

#### **B-Trees and B+ Trees**:
   - **Usage**: Used in **SQL databases** for indexing columns like `user_id`, `order_id`, `product_id`.
   - **Benefit**: Efficient search, insertion, and deletion operations.
   - **Example 1 (E-commerce Platform)**: Indexing the `order_id` in a SQL database ensures fast retrieval of order details for customers.
     - **Problem Solved**: When users want to check their order status, B+ Trees allow quick lookups in a large set of orders.
   - **Example 2 (Banking System)**: Indexing `transaction_id` for fast access to financial records.
     - **Problem Solved**: Enables quick access to transaction history during customer service queries.
   - **Architecture Placement**: In the **SQL Database** service, B-Trees would be used to index important columns (`user_id`, `transaction_id`, etc.) to support fast data retrieval.

#### **Tries (Prefix Trees)**:
   - **Usage**: Indexing **search terms**, tags, and supporting **autocomplete** features.
   - **Benefit**: Quick retrieval of entries based on prefixes.
   - **Example 1 (Search Engine)**: A user types "elec" to search for "electronics." The trie structure quickly narrows down the results by prefix.
     - **Problem Solved**: Efficiently suggests possible terms based on partially typed queries.
   - **Example 2 (Social Media Tags)**: Searching for hashtags like "#summer" in posts.
     - **Problem Solved**: Allows users to find posts by tags with fast prefix matching.
   - **Architecture Placement**: The **Trie structure** would reside in the **in-memory cache layer** (e.g., Redis) to enable fast lookups for autocomplete functionality.

#### **Inverted Indexes**:
   - **Usage**: Implemented in **search engines** like **Elasticsearch** for **full-text search**.
   - **Benefit**: Maps words to documents, enabling efficient search queries.
   - **Example 1 (Document Search Engine)**: Searching for "cloud computing" retrieves all documents containing that phrase.
     - **Problem Solved**: Enables fast full-text search across large sets of documents.
   - **Example 2 (Blogging Platform)**: Searching for specific terms in blog posts.
     - **Problem Solved**: Helps users find relevant posts based on keywords.
   - **Architecture Placement**: The **Inverted Index** is stored in the **Search Service**, which could be implemented using **Elasticsearch** or similar technology for fast text-based queries.

#### **LSM Trees (Log-Structured Merge Trees)**:
   - **Usage**: Used in **NoSQL databases** like **Cassandra** for **write-heavy workloads**.
   - **Benefit**: High write throughput by sequentially writing data to disk.
   - **Example 1 (Messaging App)**: Storing user messages where new messages are frequently written.
     - **Problem Solved**: Handles high volumes of message writes efficiently while ensuring that old messages can still be read.
   - **Example 2 (Activity Logging System)**: Storing system logs in real-time.
     - **Problem Solved**: Ensures high write performance for continuous log entries.
   - **Architecture Placement**: The **LSM-tree storage** would be part of the **Sparse Index Service**, managing metadata and ensuring efficient handling of frequent writes (e.g., user messages, logs).

   ```
               +-----------------------------------------------+
               |             Sparse Index Storage              |
               |   (LSM-tree or SSTable with sparse indexing)  |
               |                                               |
               | +-------------------------------------------+ |
               | |            Metadata (Key, Blob URL)       | |
               | |    Example:                               | |
               | |    - imageID: 1234, URL: /images/abc.jpg  | |
               | |    - imageID: 5678, URL: /images/xyz.png  | |
               | +-------------------------------------------+ |
               +-----------------------------------------------+
                                       |
                                       v
               +-----------------------------------------------+
               |              Blob Storage (e.g., S3)          |
               |     (Stores actual images, videos, etc.)      |
               | +-------------------------------------------+ |
               | |           Large Files (BLOBs)             | |
               | |    Example:                               | |
               | |    - /images/abc.jpg (binary data)        | |
               | |    - /images/xyz.png (binary data)        | |
               | +-------------------------------------------+ |
               +-----------------------------------------------+
   ```

#### **Bloom Filters**:
   - **Usage**: To quickly check for the **existence of an item**, reducing unnecessary database lookups.
   - **Benefit**: Saves I/O operations by avoiding disk access for non-existent keys.
   - **Example 1 (NoSQL Databases)**: Before performing a costly disk read, check if a key exists using a Bloom filter.
     - **Problem Solved**: Avoids unnecessary disk reads for keys that don’t exist, reducing overhead.
   - **Example 2 (Cache Lookup)**: Before hitting the database, use a Bloom filter to check if a record is in the cache.
     - **Problem Solved**: Reduces database queries, improving response time.
   - **Architecture Placement**: **Bloom filters** would reside within the **Sparse Index Service** or the **Caching Service** to avoid unnecessary lookups in databases for non-existent items.

---

### **Optimization of Read and Write Operations**:

#### **Read Optimization**:

- **Caching Layers**:
  - **Redis Cache**: Frequently accessed metadata (e.g., user profiles, product details) is stored in Redis for fast access.
  - **CDN**: Use for serving static content such as images and videos, reducing the load on blob storage.
  - **Example**: Redis is used to cache frequently requested **user profiles** in a social media app.
  - **Architecture Placement**: Redis would be part of the **Cache Service**, reducing load on the Sparse Index or Application Server.

- **Database Indexing**:
  - **Indexes on Key Fields**: Create indexes on frequently queried fields like `user_id`, `video_id`.
  - **Example**: Indexing `user_id` in a social media app for faster lookup of user data.

#### **Write Optimization**:

- **Batch Processing**:
  - **Aggregation of logs**: Write log entries in batches to reduce the number of disk writes.
  - **Example**: A logging system that batches error logs and writes them to disk at regular intervals.
  - **Architecture Placement**: Batching could be handled by the **Message Queue Service**, which aggregates logs before writing them in bulk.

- **Log-Based Storage Engines**:
  - **LSM Trees**: Store logs or messages with sequential writes.
  - **Example**: Handling high write throughput in a messaging system.

**13. Plaintext Architecture Drawing**
Draw the diagram in plain text with the following instructions 

### **4. Pick from Pre-Given Design Options**


Once a design is chosen, the next step involves:



- **Choose a Design**:  


```

Here are some design patterns to choose from:

Option: generic 

+--------------------------------------------------+
|                   User Clients                   |
|          (Web Browsers and API Consumers)        |
+--------------------------------------------------+
                   ↑                               |
                   |                               |
         [R] Read Response                 [W] Write Request
        (Redirection)                      (Shorten URL)
                   |                               |
                   |                               V
    +--------------------------------------------------+
    |                    API Gateway                   |
    |                +------------------+              |
    |                |  Rate Limiter    |              |
    |                |      [R][W]      |              |
    |                +------------------+              |
    +--------------------------------------------------+
                   ↑                               |
                   |                               |
         [R] Forward Response               [W] Forward Request
                   |                               |
                   |                               V
    +--------------------------------------------------+
    |                  Load Balancer                   |
    +--------------------------------------------------+
                   ↑                               |
                   |                               |
         [R] Forward Response               [W] Distribute Request
                   |                               |
                   |                               V
    +--------------------------------------------------+
    |               Application Servers                |
    +--------------------------------------------------+
                   ↑               |                 |
                   |               |                 |
         [R] Read from Cache       |      [W] Write to Cache
                   |               |                 |
                   |               |                 V
    +--------------+---------------+-----------------+
    |              |                                 |
    |        +-----+-----+                   +-------+-------+
    |        |   Cache   |<----------------->|    Database   |
    |        |  (Redis)  |       [R][W]      | (URL Mappings)|
    |        |  [R][W]   |                   |    [R][W]     |
    |        +-----+-----+                   +-------+-------+
    |              ^                                 ^
    |              |                                 |
    |              |                                 |
    |              |                                 |
    |              |                                 V
    |              |                          +--------------------+
    |              |                          |  Analytics Service |
    |              |                          |        [W]         |
    |              |                          +--------------------+
    |              |                                 |
    |              |                                 V
    |              |                          +--------------------+
    |              |                          | Authentication     |
    |              |                          |    Service [R]     |
    |              |                          +--------------------+
    +--------------+----------------------------------------------+
                   |
                   V
    +--------------------------------------------------+
    |                     CDN [R]                      |
    +--------------------------------------------------+
                   ↑
                   |
         [R] Serve Static Assets
                   |
                   V
    +--------------------------------------------------+
    |                   User Clients                   |
    +--------------------------------------------------+

+--------------------------------------------------+
|                    Producers                     |
+--------------------------------------------------+
               |       ↑
               |       |
       [W] Write Request
               |       |
               V       |
+--------------------------------------------------+
|                     Broker                       |
|                (Multiple Instances)              |
|          +---------------------------+           |
|          |    Partitions & Segments   |          |
|          +---------------------------+           |
|                    [W][R]                       |
+--------------------------------------------------+
               |       ↑
               |       |
   [Acknowledgment]    | [R] Read Request
               |       |
               V       |
+--------------------------------------------------+
|                 Cluster Manager                  |
+--------------------------------------------------+
               |       ↑
               |       |
               |       V
+--------------------------------------------------+
|                Consumer Manager                  |
+--------------------------------------------------+
               |       ↑
               |       |
       [W][R] to Storage (DB)
               |       |
               V       |
+--------------------------------------------------+
|      Storage (Relational Database) [R][W]        |
+--------------------------------------------------+
               |       ↑
               |       |
               |       V
+--------------------------------------------------+
|                    Consumers                     |
+--------------------------------------------------+




Option: Ticketmaster:
+---------------------------------------------+
|                   Client                    |
+---------------------------------------------+
                    ↑
                    |
                  [View Events]    [Reserve Ticket]
                    |                   |
                    V                   V
+---------------------------------------------+
|                API Gateway                  |
| - Authentication                            |
| - Rate Limiting                             |
| - Routing                                   |
+---------------------------------------------+
                    ↑
                    |
                    V
+---------------------------------------------+
|               Search Service                |
|                                             |
|  GET /search?term={term}&location={location}&type={type}&date={date} -> Partial<Event>[] |
+---------------------------------------------+
                    ↑
                    |
                    V
+---------------------------------------------+         +-------------------------+
|             Elasticsearch                  |         |      Redis (Event Cache) |
| - Index on name, description,               |<------->|                         |
|   venue, performer, date                    |         +-------------------------+
| - Node query caching enabled                |
+---------------------------------------------+
                    ↑
                    |
                    V
+---------------------------------------------+         +--------------------------+
|               Database (PostgreSQL)         |<------->|    Redis (Ticket Lock)    |
| - Event                                      |         | TTL: 10 min              |
|    - id, venueId, performerId, tickets[]     |         +--------------------------+
|    - name, description, etc.                 |
| - Venue                                      |
|    - id, location, seatMap                   |
| - Performer                                  |
|    - id, name, etc.                          |
| - Ticket                                     |
|    - id, eventId, seat, price, status        |
| - Booking                                    |
|    - id, userId, tickets[]                   |
+---------------------------------------------+
                    ↑
                    |
                    V
+---------------------------------------------+
|               Event Service                 |
|                                             |
| view(eventId)                               |
+---------------------------------------------+
                    ↑
                    |
                    V
+---------------------------------------------+
|               Booking Service               |
| - Virtual Waiting Queue                     |
| - Ticket Lock                               |
|                                             |
| reserve(ticketId, userId)                   |
| confirm(ticketId, userId, paymentDetails)   |
+---------------------------------------------+
                    ↑
                    |
                    V
+---------------------------------------------+
|                  Stripe                     |
| - Payment Processing                        |
+---------------------------------------------+

Option: Uber:


+---------------------------------------------+
|           Rider Client (iOS/Android)        |
+---------------------------------------------+
                    ↑
                    |
      getFareEstimate(), acceptOrDeclineRide()
                    |
                    V
+---------------------------------------------+
|       API Gateway & Load Balancer           |
| - Routing                                   |
| - Authentication                            |
| - Rate Limiting                             |
+---------------------------------------------+
                    ↑
                    |
                    V
+---------------------------------------------+
|                Ride Service                 |
| - Handles fare estimation                   |
| - Handles ride creation                     |
| - Triggers matching workflow                |
| - Updates DB with driver accept/decline     |
+---------------------------------------------+
                    ↑              ↑             ↑
                    |              |             |
                    |              |             |
       +------------+              |             |
       |                           |             |
       |             +-------------------+       |
       |             | Match Queue        |       |
       |             +-------------------+       |
       |                           |             |
       |                           |             |
       |                           |             |
       |                           V             |
       |             +-------------------+       |
       |             | Ride Matching      |       |
       |             | Service            |       |
       |             | - Matches drivers  |       |
       |             |   and riders       |       |
       |             +-------------------+       |
       |                           |             |
       |                           |             |
       |                           V             |
       |             +-------------------+       |
       |             | Location Service   |       |
       |             | - Stores drivers'  |       |
       |             |   current locations|
       |             |   as lat/long      |       |
       |             +-------------------+       |
       |                           |             |
       |                           |             |
       V                           V             V
+-----------------------+    +--------------------------+
|   Distributed Lock     |    |   Location DB (Redis)    |
|   (Redis)              |    | - Fetch closest drivers  |
| - Check if a driver    |    |                          |
|   has an outstanding   |    |                          |
|   ride request         |    |                          |
+-----------------------+    +--------------------------+

                    ↑
                    |
                    V
+---------------------------------------------+
| DB (DynamoDB or PostgreSQL)                 |
| - Stores Fare & Ride objects                |
|   Ride:                                     |
|     - rideId, riderId, driverId, fareId,    |
|       source, destination, status, etc.     |
|   Fare:                                     |
|     - id, userId, source, destination,      |
|       price, eta, etc.                      |
+---------------------------------------------+
                    |
                    V
+---------------------------------------------+
|       Notification Service (APN, FCM)       |
| - Send driver notification to               |
|   accept/decline ride                       |
+---------------------------------------------+
                    |
                    V
+---------------------------------------------+
|        Driver Client (iOS/Android)          |
+---------------------------------------------+

+---------------------------------------------+
|          3rd Party Mapping Service          |
+---------------------------------------------+
                    ↑
                    |
                    |
                    V
+---------------------------------------------+
|               Ride Service                  |
+---------------------------------------------+


Option:Dropbox

+---------------------------------------------+
|                Uploader                     |
| - Chunk file on client & calculate          |
|   fingerprints                              |
+---------------------------------------------+
                    ↑
                    |
                    V
+---------------------------------------------+
|        API Gateway & Load Balancer          |
| - Routing                                   |
| - Authentication                            |
| - Rate Limiting                             |
+---------------------------------------------+
                    ↑                ↑
                    |                |
        Download File                | getPresignedUrl()
                    |                |
                    V                |
+---------------------------------------------+         +-----------------------+
|                 Downloader                  |         |         S3            |
+---------------------------------------------+         | - Stores the actual   |
                    ↑                ↑                 |   file                |
                    |                |                 +-----------------------+
                    |                |                        ↑
                    |                |                        |
                    V                |                        |
+---------------------------------------------+         +-----------------------+
|               File Service                  |<--------|   File Metadata DB    |
|                                             |         | - FileMetadata        |
|                                             |         |   - fileId (PK)       |
|                                             |         |   - name              |
+---------------------------------------------+         |   - size              |
                    |                                  |   - mimeType          |
                    |                                  |   - uploadedBy        |
                    |                                  |   - status            |
                    |                                  |   - s3Url (link to     |
                    |                                  |     file in S3)        |
                    V                                  |   - chunks: Chunk[]    |
+---------------------------------------------+         |                       |
|                    CDN                      |<--------| - SharedFiles         |
| - Cache frequently accessed files            |         |   - userId (PK)       |
+---------------------------------------------+         |   - fileId            |
                                                        +-----------------------+
                               
                    ↑                                  
                    |
                    V
+-------------------------------------------------------------+
|              Upload file chunks directly to S3               |
|                via pre-signed URL                            |
+-------------------------------------------------------------+
                    ↑
                    |
                    |
+-------------------------------------------------------------+
|         S3 Notification on completed upload                 |
+-------------------------------------------------------------+

Option:Ad Click Aggregator

+---------------------------------------------+
|                  Browser                    |
+---------------------------------------------+
                    ↑
                    |
                    V
+---------------------------------------------+
|          LB & API Gateway                   |
+---------------------------------------------+
                    ↑
                    |         [Generate Ad Impression ID as Idempotency Key]
                    |         and store in cache
                    |
                    V
+---------------------------------------------+         +-----------------------+
|        Ad Placement Service                 |<------->|        Ad DB           |
+---------------------------------------------+         | - Advertisement:       |
                    ↑                                  |   - AdId                |
                    |                                  |   - RedirectUrl         |
                    |                                  |   - ... metadata        |
                    |                                  +-----------------------+
                    |
                    V
+---------------------------------------------+
|                  Cache                      |
+---------------------------------------------+
                    |
                    |
                    V
+---------------------------------------------+
|            Click Processor                  |
+---------------------------------------------+
                    |
                    |
                    V
+---------------------------------------------+
|                Kinesis                      |
| - Partition by AdId (Celebrity Solution     |
|   for Hot Shards)                           |
| - 7-day retention period on Kinesis         |
+---------------------------------------------+
                    |
                    |
                    V
+---------------------------------------------+         +-----------------------+
|                Flink                        |<------->|  Raw Click Data (S3)   |
+---------------------------------------------+         +-----------------------+
                    |
                    |
                    V
+---------------------------------------------+         +-----------------------+
|               OLAP DB                       |<------->|   Reconciliation       |
| - Aggregated Data:                          |         |   Worker               |
|   - AdId                                    |         | - Potentially fix      |
|   - AdvertiserId (PK)                       |         |   incorrect records    |
|   - Minute (SK)                             |         +-----------------------+
|   - Click Count                             |
+---------------------------------------------+
                    ↑
                    |
                    |
+---------------------------------------------+
|           Analytics Service                 |
+---------------------------------------------+
                    ↑
                    |
                    |
+---------------------------------------------+
|           Analyst Browser                   |
+---------------------------------------------+

                    |
                    V
+---------------------------------------------+
|               Spark (Map Reduce)            |
| - Scheduled by cron                         |
+---------------------------------------------+

Option:Web Crawler
+---------------------------------------------+
|                    DNS                      |
+---------------------------------------------+
                    ↑
                    |
                    V
+---------------------------------------------+
|                 Webpage                     |
+---------------------------------------------+
                    ↑
                    |
                    V
+---------------------------------------------+         +-----------------------+
|                 Crawler                     |<------->|       S3 HTML Data    |
| - Fetch & store webpage                     |         | - Stores raw HTML     |
+---------------------------------------------+         +-----------------------+
                    ↑                                  |
                    |                                  |
                    | Save raw HTML                    |
                    |                                  |
                    V                                  |
+---------------------------------------------+         +-----------------------+
|              URL Metadata                   |<------->|   Parsing Queue (SQS)  |
| - URL:                                      |         +-----------------------+
|   - id, url, s3Link, lastCrawlTime,         |               ↑
|     hash (GSI), depth                       |               |
| - Domain:                                   |               |
|   - domain, lastCrawlTime, robots           |               |
+---------------------------------------------+               |
                    ↑                                       |
                    | Update URL status                     |
                    |                                       |
                    V                                       |
+---------------------------------------------+              | Fetch s3Url
|      Frontier Queue (SQS)                   |              |
| - Retry on failure w/ backoff               |              |
| - Put extracted URLs back on the queue      |              |
+---------------------------------------------+              V
                    ↑                                  +-----------------------+
                    |                                  |    Parsing Worker      |
                    |                                  | - Download HTML from S3 |
                    |                                  | - Save parsed text to S3|
                    |                                  | - Check hash first      |
                    |                                  +-----------------------+
                    |
                    |
                    V
+---------------------------------------------+
|          Rate Limiter & DNS Cache           |
| - Use same Redis cluster                    |
+---------------------------------------------+

+---------------------------------------------+
|                  DLQ                        |
+---------------------------------------------+

Option:Top-K Youtube Videos
+---------------------+         +----------------------+         +--------------------+         +--------------------+
|     Load Balancer    | ----->  |    Top-K Service      | ----->  |     Top-K Cache     | ----->  |    Snapshot (Blob   |
|                     |         |                      |         |                    |         |      Storage)       |
+---------------------+         +----------------------+         +--------------------+         +--------------------+
                                      |                                          |                              |
                                      V                                          V                              V
                            +---------------------+               +---------------------+         +---------------------+
                            |      Counter        |               |      Counter        |         |   View Stream (Kafka)|
                            | +----------------+  |               | +----------------+  |         +---------------------+
                            | | Hour Top-K Heap |  |               | | Hour Top-K Heap |  |
                            | +----------------+  |               | +----------------+  |
                            | +----------------+  |               | +----------------+  |
                            | |   Hour Counts   |  |               | |   Hour Counts   |  |
                            | +----------------+  |               | +----------------+  |
                            | +----------------+  |               | +----------------+  |
                            | |  Day Top-K Heap |  |               | |  Day Top-K Heap |  |
                            | +----------------+  |               | +----------------+  |
                            | +----------------+  |               | +----------------+  |
                            | |   Day Counts    |  |               | |   Day Counts    |  |
                            | +----------------+  |               | +----------------+  |
                            +---------------------+               +---------------------+


Option:leetcode:

+----------------------------+       +---------------------+       +--------------------------------------------------------+
|    Client - Simple          | ----> |    Primary Server    | ----> |             Primary DB (DynamoDB)                      |
|    Monaco IDE               |       |                     |       |   Problem:                                              |
|  - GET /problems            |       |                     |       |   - id, description, code stubs, test cases             |
|  - GET /problem/{id}        |       |                     |       |   - metadata                                            |
|  - POST /problems/{id}/submit|       |                     |       |   Submissions:                                          |
|  - GET /leaderboard/{compId} |       |                     |       |   - id, submittedAt, test case results, runtime         |
|  - GET /check/{id}           |       |                     |       |   - error?                                              |
+----------------------------+       +---------------------+       +--------------------------------------------------------+
                |                                                           |
                |                                                           V
                |                                          +-------------------------------+
                |                                          |   Redis (Leaderboard Sorted Set)|
                |                                          +-------------------------------+
                |
                V
+--------------------------------------------------------+
|                     AWS SQS                            |
|          (Queue for processing submissions)            |
+--------------------------------------------------------+
                |
                V
+--------------------------------------------------------+
|                       Worker                           |
|  - Processes submission data                           |
|  - Executes test cases using runtime services          |
+--------------------------------------------------------+
                |
                V
+---------------------------------------------------------------------------------------------------+
|                                  AWS Fargate                                                      |
|                                                                                                   |
| +-------------------+   +-------------------+   +-------------------+   +---------------------+   |
| | Java Runtime       |   | Python Runtime    |   | JavaScript Runtime|   | X Runtime Service   |   |
| | Service            |   | Service           |   | Service           |   |                     |   |
+----------------------+   +-------------------+   +-------------------+   +---------------------+   |
                                                                                                   |
|                         Security:                                                                |
|                         - Docker for isolation                                                   |
|                         - Read-only filesystem (writes to tmp)                                   |
|                         - CPU and memory bounds                                                  |
|                         - Explicit timeout per execution                                         |
|                         - VPC for network controls                                               |
|                         - No system calls (seccomp)                                              |
|                         - Configured with necessary runtime environments, test harnesses,        |
|                           and language dependencies                                              |
+---------------------------------------------------------------------------------------------------+



Option:Facebook live comments 

+-----------------------+                +-----------------+                +-------------------------+
| Commenter Client       |<-------------->|  Load Balancer  |<-------------->| Comment Management       |
|                       |                |                 |                | Service                  |
| - for /create (HTTPS)  |                |                 |                |                         |
| - for comment          |                |                 |                | - When a new comment     |
|   broadcasting (SSE)   |                |                 |                |   is posted, persist in  |
+-----------------------+                +-----------------+                |   DB and send over SSE   |
                                                                              |   to connected clients   |
                                                                              +-------------------------+
                                                                                       |
                                                                                       V
+---------------------+                 +-----------------------------+                +-----------------------------+
| Pagination Cache    |<--------------->|   Comments DB (DynamoDB)     |<-------------->|  Comment Management Service  |
| (Redis)             |                 |                               |                |                             |
| - Keeps fetched     |                 |   - commentId                |                | - Stores data persistently  |
|   paged comments    |                 |   - videoId (PK)             |                |   and serves client         |
|   in memory with    |                 |   - content                  |                |   requests                  |
|   short TTL to      |                 |   - author                   |                +-----------------------------+
|   improve infinite  |                 |   - createdAt (SK)           |
|   scrolling         |                 |                               |
+---------------------+                 +-----------------------------+


+---------------------+                     +---------------------+                       +--------------------+
|     API Gateway      |<------------------->|     Post Service     |<-------------------->|  Post Create Queue  |
| - Entry point for    | - /createPost (POST)| - Processes new      |                       | - Holds newly created|
|   all client requests|                     |   post creation      |                       |   posts before      |
| - Routes API calls   |                     | - Caches post in     |                       |   processing        |
|   to appropriate     |                     |   Redis and persists |                       +--------------------+
|   services           |                     |   post in DynamoDB   |
|                     |                     | - Sends new posts    |
+---------------------+                     |   to Post Queue      |
      |                                     +---------------------+                               
      V                                                                                             
+---------------------+                     +---------------------+                       +--------------------+
|   Follow Service    |<------------------->|     Feed Service     |<-------------------->|    Feed Workers     |
| - /follow (POST)    | - /getFeed (GET)    | - /updateFeed (POST) |                       | - Process posts from|
| - Manages follow    | - Retrieves feed    | - Retrieves feeds for|                       |   Post Queue        |
|   relationships     |   data for user     |   users              |                       | - Update user feeds |
| - /unfollow (POST)  | - Interacts with    | - Updates feeds when |                       |   in DynamoDB and   |
| - Updates follow    |   Redis and         |   new posts are      |                       |   Redis             |
|   relationships in  |   DynamoDB          |   created or updated |                       +--------------------+
|   DynamoDB Follow   |                     | - Caches feed data in|                                |
|   Table             |                     |   Redis              |                                |
+---------------------+                     +---------------------+                                |
      |                                            |                                                  |
      V                                            V                                                  V
+---------------------+                     +---------------------+                       +--------------------+
|  Redis (Feed Cache) |                     |   Redis (Post Cache) |                       |     DynamoDB        |
| - Caches user feed  |                     | - Caches post data   |                       | - Post Table        |
|   data to improve   |                     |   for quicker access |                       |   - Stores post     |
|   response times    |                     | - Reduces load on    |                       |     information     |
| - Used by Feed      |                     |   DynamoDB Post Table|                       | - Feed Table        |
|   Service for quick |                     |                      |                       |   - Stores feed data|
|   feed retrieval    |                     |                      |                       |     for users       |
+---------------------+                     +---------------------+                       | - Follow Table      |
                                                                                          |   - Stores follow    |
                                                                                          |     relationships    |
                                                                                          +--------------------+

Option:Facebook newsfeed


+---------------------+                     +---------------------+                       +--------------------+
|     API Gateway      |<------------------->|     Post Service     |<-------------------->|  Post Create Queue  |
| - Entry point for    | - /createPost (POST)| - Processes new      |                       | - Holds newly created|
|   all client requests|                     |   post creation      |                       |   posts before      |
| - Routes API calls   |                     | - Caches post in     |                       |   processing        |
|   to appropriate     |                     |   Redis and persists |                       +--------------------+
|   services           |                     |   post in DynamoDB   |
|                     |                     | - Sends new posts    |
+---------------------+                     |   to Post Queue      |
      |                                     +---------------------+                               
      V                                                                                             
+---------------------+                     +---------------------+                       +--------------------+
|   Follow Service    |<------------------->|     Feed Service     |<-------------------->|    Feed Workers     |
| - /follow (POST)    | - /getFeed (GET)    | - /updateFeed (POST) |                       | - Process posts from|
| - Manages follow    | - Retrieves feed    | - Retrieves feeds for|                       |   Post Queue        |
|   relationships     |   data for user     |   users              |                       | - Update user feeds |
| - /unfollow (POST)  | - Interacts with    | - Updates feeds when |                       |   in DynamoDB and   |
| - Updates follow    |   Redis and         |   new posts are      |                       |   Redis             |
|   relationships in  |   DynamoDB          |   created or updated |                       +--------------------+
|   DynamoDB Follow   |                     | - Caches feed data in|                                |
|   Table             |                     |   Redis              |                                |
+---------------------+                     +---------------------+                                |
      |                                            |                                                  |
      V                                            V                                                  V
+---------------------+                     +---------------------+                       +--------------------+
|  Redis (Feed Cache) |                     |   Redis (Post Cache) |                       |     DynamoDB        |
| - Caches user feed  |                     | - Caches post data   |                       | - Post Table        |
|   data to improve   |                     |   for quicker access |                       |   - Stores post     |
|   response times    |                     | - Reduces load on    |                       |     information     |
| - Used by Feed      |                     |   DynamoDB Post Table|                       | - Feed Table        |
|   Service for quick |                     |                      |                       |   - Stores feed data|
|   feed retrieval    |                     |                      |                       |     for users       |
+---------------------+                     +---------------------+                       | - Follow Table      |
                                                                                          |   - Stores follow    |
                                                                                          |     relationships    |
                                                                                          +--------------------+

Option:Facebook post search
+-----------------------+                     +-----------------------+                       +------------------------+
|     Post Service       |                     |      Like Service      |                       |      Like Batcher       |
| - /createPost          |                     | - /likePost            |                       | - Batches likes for     |
| - Sends post creation  |                     | - Sends like events    |                       |   ingestion             |
|   requests             |                     |                       |                       +------------------------+
+-----------------------+                     +-----------------------+                               |
       |                                      create Posts               create Likes                  V
       |                                               \                     /                       +------------------------+
       |                                                \                   /                        |        Kafka           |
       |                                                 \                 /                         | - Asynchronous event   |
       |                                                  V               V                          |   stream for posts and |
       |                                              +-------------------------+                    |   likes                |
       |                                              |      Load Balancer       |                    +------------------------+
       |                                              +-------------------------+                             |
       |                                                        |                                             |
       |                                                        |                                             |
       V                                                        |                                             V
+--------------------+                                  +------------------------+                    +------------------------+
|     Event Writer   | -------------------------------->|      Ingestion Service  |<------------------|    Cold Indexes         |
|  - Writes events   |                                  | - Processes events     |                    |    (Blob Storage)       |
|    to Kafka        |                                  |   and updates indexes  |                    +------------------------+
+--------------------+                                  +------------------------+
                                                                     |
                                                                     |
                                                                     V
                                                           +---------------------+
                                                           |     Index (Redis)    |
                                                           | - Stores PostId by   |
                                                           |   Creation, Likes    |
                                                           |   for search queries |
                                                           +---------------------+
                                                                     |
                                                                     |
                                                                     V
+---------------------+                      +---------------------+                        +---------------------+
|    Search Cache     |<-------------------->|   Search Service     |<----------------------|     API Gateway      |
|    (Redis)          |                      | - Executes search    |                       |  - Routes requests  |
| - Caches search     |                      |   queries based on   |                       |    to search service|
|   results to reduce |                      |   keyword, likes, or |                       +---------------------+
|   load              |                      |   time               |
+---------------------+                      +---------------------+
                                                                     |
                                                                     |
                                                                     V
+---------------------+                      +---------------------+
|        Client       |                      |      CDN             |
| - Initiates search  |                      | - Serves static      |
|   query via API     |                      |   assets and         |
|                     |                      |   caches results     |
+---------------------+                      +---------------------+




Option:Local delivery like doordash, uber eats, gopuff

+---------------------------+                     +-------------------------+                      +------------------------+
|        API Gateway         |                     |      Availability       |                      |      Inventory Table    |
| - Entry point for requests |                     |       Service           |<-------------------->| - Stores inventory data|
| - Routes order and         |<------------------->| - Fetches item quantity  |                      | - Sharded by region ID |
|   availability queries     |                     |   for items              |                      | - Read replicas for    |
|                            |                     | - Queries Inventory Table|                      |   faster reads         |
+---------------------------+                     |   for availability across|                      +------------------------+
     |                                             |   nearby distribution    |                                 ^
     |                                             |   centers (DCs)          |                                 |
     |                                             +-------------------------+                                 |
     |                                                      |                                               |
     |                                                      | Check item availability                       |
     |                                                      |                                               |
     V                                                      V                                               V
+------------------------+                      +---------------------------+                   +------------------------+
|   GET /availability    |                      |      Nearby Service        |<---------------->|      Postgres Leader    |
|   ?lat=LAT&long=LONG   |                      | - Finds nearby DCs         |                   | - Main database for    |
|   &items=ITEM1,ITEM2   |                      | - Integrates with Travel   |                   |   transactions         |
|   Fetch availability   |                      |   Time Service for DC ETA  |                   | - Handles write ops for|
+------------------------+                      +---------------------------+                   |   Inventory and Orders |
                                                                                                 +------------------------+
                                                                                                 +------------------------+
                                                                                                 |   Orders Table          |
                                                                                                 | - Stores all order data |
                                                                                                 +------------------------+
                                                     |
                                                     | Fetch travel time from DC to customer
                                                     V
                                          +---------------------------+
                                          |      Travel Time Service   |
                                          | - Estimates time from      |
                                          |   nearest DC to delivery   |
                                          |   address                  |
                                          +---------------------------+

                                                     |
                                                     |
                                                     V
                                          +---------------------------+
                                          |      Orders Service        |
                                          | - Receives orders and writes|
                                          |   them to the Orders Table |
                                          | - Queries Availability for |
                                          |   stock availability       |
                                          | - Writes order transactions|
                                          +---------------------------+
                                                     ^
                                                     |
                                                     |
                                                     |
+------------------------+                      +---------------------------+
|   POST /order          |                      |      Availability Service  |
|   {                    |<------------------->| - Checks stock levels for  |
|    lat: LAT,           |                      |   new orders               |
|    long: LONG,         |                      +---------------------------+
|    items: ITEM1,       |
|            ITEM2,      |
|   }                    |
|   Place new order      |
+------------------------+


Option:Tinder: (dating app)

+-----------------------------+                +-----------------------------+                     +-----------------------------+
|           Client            |                |   API Gateway & Load Balancer|                     |   Push Notification Service |
|  - swipe(yes/no)            |                | - Routing                    |                     | - Sends notifications for   |
|  - getStack()               |                | - Authentication             |                     |   matches via APNs/FCM      |
|  - setProfile()             |                | - Rate Limiting              |                     |                             |
|                             |                | - Routes to Profile/Swipe    |                     |                             |
|  User interacts with app    |--------------->|   services                   |<-------------------+                             |
+-----------------------------+                +-----------------------------+                     +-----------------------------+
                                                                                                    ^
                                                                                                    |
                                                                                                    |
                                                                                                    |  Sends push notifications for
                        swipe(yes/no), getStack(), setProfile()                                     |  matches
                                     |                                                              |
                                     V                                                              |
+-----------------------------+                +-----------------------------+                     +-----------------------------+
|        Swipe Service        |                |       Profile Service       |                     |       Swipe Cache           |
| - Handles swipe actions     |                | - setProfile(), getStack()  |                     | - Bloom filter for swipes   |
| - swipe(yes/no)             |<--------------+| - Fetches profiles          |<-------------------+| - Ensures no double swipes  |
+-----------------------------+                +-----------------------------+                     +-----------------------------+
           |                                            | setProfile()                             |
           | swipe(yes/no)                              | getStack()                               |
           V                                            V                                          |
+-----------------------------+                +-----------------------------+                     |
| Swipe Code Blockifier       |                |        Stack Cache          |                     |
| - Batch processes swipes    |                | - Stores cached profiles    |                     |
| - Manages matches           |                | - Optimizes swipe actions   |                     |
+-----------------------------+                | - Preloads nearby stacks    |                     |
           |                                    +-----------------------------+                     |
           | match found                                   ^                                       |
           |                                               |                                       |
           V                                               |                                       |
+-----------------------------+                +-----------------------------+                     |
| Push Notification Service   |                |       Cron Service           |                     |
| - Sends notifications via   |                | - Prefetches profiles        |                     |
|   APNs/FCM for matches      |                | - Runs periodic background   |                     |
+-----------------------------+                |   tasks for caching          |                     |
           ^                                    +-----------------------------+                     |
           |                                                                                           
           |                                                                                           
+-----------------------------+                +-----------------------------+                     +-----------------------------+
|        Swipe DB             |                |        ElasticSearch        |                     |       Profile DB            |
| - Stores swipe records      |                | - Search for profiles       |                     | - Stores user profiles      |
| - {user1, user2, liked}     |                | - Updates swipe queries     |                     | - {name, agePref,           |
+-----------------------------+                +-----------------------------+                     |    genderPref, maxDistance} |
           ^                                                                                       +-----------------------------+
           |                                                                                           
           |                                                                                           
+-----------------------------+                                                                         
|       Swipe Service         |                                                                         
+-----------------------------+                                                                         


Option:Whatsapp:
+-----------------------------+                +-----------------------------+                +-----------------------------+
|           Client            |                |       Load Balancer          |                |         Chat Server         |
| - Sends/receives messages   |                | - Distributes traffic to     |                | - Manages chat sessions     |
| - Connects to chat sessions |--------------->|   chat servers               |--------------->| - Handles real-time messages|
| - Multiple clients connect  |                | - Provides horizontal        |                | - Communicates with Redis   |
+-----------------------------+                |   scalability                |                |   Pub/Sub and Database      |
                                                +-----------------------------+                +-------------+---------------+
                                                                                                             |
                                                                                                    Handles client messages
                                                                                                             |
                                                                                                             V
+-----------------------------+                +-----------------------------+                +-----------------------------+
|     Participant Cache        |<-------------->|        Redis Pub/Sub         |<-------------->|    Database (MySQL)         |
| - Stores active participants |                | - Distributes chat events    |                | - Stores chat logs,         |
|   for quick access           |                |   between chat servers       |                |   participants, messages,   |
| - Used to reduce load on DB  |                | - Synchronizes servers       |                |   and events                |
+-----------------------------+                +-----------------------------+                | - Tables:                   |
                                                                                               |   - Chat                    |
                                                                                               |   - Chat Participant        |
                                                                                               |   - Message                 |
                                                                                               |   - Events                  |
                                                                                               +-----------------------------+
                                                                                                            ^
                                                                                                            |
                                                                                                            |
                                                                                             Publishes/receives events
                                                                                                            |
                                                                                                            V
+-----------------------------+                                                                      +-----------------------------+
|       Cleanup Service        |                                                                      |   Presence Service          |
| - Periodically clears        |                                                                      | - Monitors online status    |
|   inactive participants      |                                                                      | - Updates Participant Cache |
+-----------------------------+                                                                      +-----------------------------+
                                                                                                            ^
                                                                                                            |
                                                                                                            |
                                                                                                 Updates participant status
                                                                                                            |
                                                                                                            V
                                                                                                +-----------------------------+
                                                                                                |     Participant Cache        |
                                                                                                | - Stores active participants |
                                                                                                |   for quick access           |
                                                                                                | - Used to reduce load on DB  |
                                                                                                +-----------------------------+

Option:Youtube

+-----------------------------+                +-----------------------------+                +-----------------------------+
|           Client            |                |     API Gateway &            |                |            CDN               |
| - Uploads video files       |                |      Load Balancer           |                | - Caches frequently accessed |
| - Requests video segments   |<-------------->| - Routing, Authentication,   |<-------------->|   video segments             |
| - Downloads video segments  |                |   Rate Limiting              |                | - Delivers video manifest/   |
|                             |                | - Routes requests to Video   |                |   segments to client         |
+-----------------------------+                |   Service                    |                +-----------------------------+
                                               +-----------------------------+                                             
                                                         |                                   
       Uploads video via pre-signed URL / Requests video manifest and segments
                                                         |
                                                         V
+-----------------------------+                +-----------------------------+                +-----------------------------+
|       Video Service          |                |             S3               |                |   Upload Monitor (Lambda)    |
| - Provides pre-signed URLs   |                | - Stores uploaded video      |<---------------| - Receives S3 event          |
| - Manages video uploads      |--------------->|   files, manifest files,     |                |   notifications             |
| - Handles requests for video |                |   and metadata               |                | - Triggers processing flow   |
|   manifest                   |                | - Allows direct upload via   |                +-----------------------------+
+-----------------------------+                |   pre-signed URLs            |
                                               +-----------------------------+
                                                              |
                                                    Stores processed files
                                                              |
                                                              V
+-----------------------------+                +-----------------------------+                +-----------------------------+
|  Video Processing Service    |                |    Video Metadata DB        |                |     Video Metadata Cache     |
| - Processes uploaded video   |--------------->| - Stores metadata for videos|<-------------->| - Caches video metadata      |
|   files: splitting,          |                | - Metadata includes:        |                |   for quick access           |
|   transcoding, audio         |                |   videoId, uploaderId, name |                |                             |
|   processing, transcript     |                |   description, chunks, URLs |                |                             |
|   generation                 |                +-----------------------------+                +-----------------------------+
| - Builds and stores manifest |
|   files in S3                |
+-----------------------------+
            |                                                        ^
            | Stores manifest files                                  |
            V                                                        |
+-----------------------------+                +-----------------------------+                +-----------------------------+
|             S3              |<---------------+    Build Manifest Files      |                |            CDN              |
| - Stores manifest files     |                | - Generates video manifest   |                | - Caches and delivers video |
+-----------------------------+                |   files based on the video   |                |   manifest and segments     |
                                                | - Marks video upload as      |                +-----------------------------+
                                                |   complete                   |
                                                +-----------------------------+
            ^                                                        |
            |                                                        |
+-----------------------------+                +-----------------------------+
|    Audio Processing          |                |   Transcript Generation     |
| - Processes audio tracks     |                | - Generates transcript files |
|   within the video           |                |   for the video              |
+-----------------------------+                +-----------------------------+
            ^                                                        |
            |                                                        |
+-----------------------------+                +-----------------------------+
|       Transcoding            |                |        Video Splitter        |
| - Transcodes video into      |                | - Splits video into chunks   |
|   multiple resolutions       |                | - Stores chunks in S3        |
| - Stores transcoded chunks   |                +-----------------------------+
|   in S3                      |
+-----------------------------+
            ^
            |
+-----------------------------+
| Video Processing Service    |
+-----------------------------+

```



Do a Deep dive into the core component / the core requirement is fulfilled 

Deep Dive into Core Component Fulfillment
Core Requirement:
The core requirement is to design a scalable and efficient system that handles real-time updates (e.g., status updates, posts, notifications) while maintaining consistency, ensuring fast retrieval, and supporting a large number of users.

1. Efficient Data Storage and Retrieval
The system needs to support fast reads and writes for data like user statuses, posts, or notifications. This core requirement can be fulfilled by leveraging sharding for data distribution and in-memory caching for low-latency retrieval.
Sharding Strategy:
Core Design: Data is sharded based on a key, such as user_id, to ensure even distribution across nodes. This allows the system to scale horizontally, handling more users without overwhelming any single database node.
Details:
Each shard contains a portion of the data, such as user statuses. For instance, if the system has 100 shards, a user’s data is stored in one specific shard based on their user_id, using consistent hashing to determine which shard.
By sharding the data in this way, the system ensures localized reads and writes, reducing cross-shard operations. Sharding also reduces contention on a single database, improving performance as the system scales.
Fulfillment:
This approach ensures that reads and writes are spread evenly across nodes, allowing the system to handle large volumes of user updates while maintaining fast access times.
Caching Strategy:
Core Design: Frequently accessed data, such as recent statuses or posts, is cached in-memory (e.g., using Redis) to provide fast, low-latency access.
Details:
When a user requests their status or a feed of posts, the system first checks the cache. If the data is found, it’s returned immediately, avoiding a costly database lookup.
If the cache misses, the system retrieves the data from the appropriate shard, caches it for future requests, and then serves the data to the user.
Fulfillment:
Caching ensures the system meets the requirement of fast data retrieval, reducing the load on the database and ensuring quick responses for frequently accessed data, such as user statuses or popular posts.

2. Asynchronous Processing for High Throughput
Given the need for real-time updates, handling a high volume of concurrent writes (e.g., status updates, post creation) without blocking user requests is crucial. This core requirement is fulfilled through message queues and background processing.
Message Queue Design:
Core Design: Asynchronous processing using message queues (e.g., Kafka, RabbitMQ) allows the system to decouple user interactions from heavy backend operations.
Details:
When a user updates their status, the system immediately adds this request to a message queue. This ensures that the user's request is acknowledged quickly without waiting for the database operation to complete.
Workers subscribed to the message queue process these updates in the background, updating the database asynchronously.
Fulfillment:
This design allows the system to handle a high volume of write operations efficiently. By processing these operations in the background, the system ensures high throughput without blocking user interactions, thus fulfilling the core requirement of handling real-time updates at scale.

3. Consistency and Data Integrity
The system must ensure consistency for user updates, even in a distributed environment where data is replicated across multiple nodes or regions. To meet this core requirement, the system can use eventual consistency combined with mechanisms like quorum-based replication.
Replication Strategy:
Core Design: Data is replicated across multiple nodes to ensure fault tolerance and high availability. Each shard has multiple replicas (e.g., 3 replicas), with one replica acting as the leader and others as followers.
Details:
When a user updates their status, the system writes the update to the leader replica and asynchronously replicates it to the follower replicas. This ensures the system remains available even if some replicas are temporarily out of sync.
To balance availability and consistency, the system can use quorum-based writes. For example, a status update is considered successful once a majority of replicas (e.g., 2 out of 3) acknowledge the write.
Fulfillment:
This approach ensures eventual consistency across the system, meaning that while replicas may be temporarily out of sync, they will eventually converge. This meets the core requirement by ensuring that updates are durable and the system remains available, even in the event of failures or network partitions.
Handling Stale Data:
Core Design: To mitigate the impact of stale data, the system can implement a read-your-own-writes mechanism, ensuring that users always see their most recent updates, even if other users might see slightly stale data.
Details:
When a user updates their status, the system stores the most recent version in a cache specific to that user. This ensures that any subsequent reads by the same user return the latest data, even if replication to followers is still in progress.
Fulfillment:
This design ensures that users always see their own most recent updates, improving the user experience while still maintaining the benefits of eventual consistency for the rest of the system.

4. Scalability and Fault Tolerance
The core requirement for scalability and fault tolerance is met by designing the system to scale horizontally and handle failures gracefully.
Horizontal Scaling:
Core Design: The system scales by adding more nodes (or shards) to distribute the load. As more users join the system, additional shards are added to handle the increased data volume.
Details:
Consistent hashing ensures that data is evenly distributed across new nodes, minimizing the impact of scaling events.
The system can dynamically scale both database nodes and worker nodes (which process message queues) to handle spikes in traffic or data volume.
Fulfillment:
Horizontal scaling allows the system to grow without performance degradation, fulfilling the core requirement of scalability.
Fault Tolerance:
Core Design: Fault tolerance is achieved by replicating data across multiple nodes and using techniques like leader election to promote followers to leaders in the event of a failure.
Details:
If a leader node fails, one of the follower nodes is automatically promoted to the leader role, ensuring that the system remains operational.
By using multiple replicas and spreading them across different data centers or regions, the system can withstand localized failures without affecting overall availability.
Fulfillment:
This design ensures that the system remains highly available, even in the face of node failures or network partitions, fulfilling the core requirement for fault tolerance.




Draw a new plaintext diagram to showcase just the deep dive / core component

```

Viewer                                      Cache                                  Database
-----------------------------------------------------------------------------------------------
|                                            |                                        |
| Viewer requests initial comments on join   |                                        |
|------------------------------------------->|                                        |
| GET /comments/liveVideoId?person=uid&page=1|                                        |
|   &pagesize=10                             |                                        |
|                                            |                                        |
|                [Cache Miss]                |                                        |
|                                            |--------------------------------------->|
|                                            |   Query next set of comments based    |
|                                            |   on cursor                           |
|                                            |<---------------------------------------|
|                                            |   Return next set of comments and     |
|                                            |   update cursor                       |
|                                            |                                        |
|<-------------------------------------------|                                        |
| Return initial set of comments             |                                        |
|                                            |                                        |
|                [Cache Hit]                 |                                        |
|<-------------------------------------------|                                        |
| Return initial set of comments             |                                        |
|                                            |                                        |
| Viewer scrolls for more comments           |                                        |
|------------------------------------------->|                                        |
| GET /comments/liveVideoId?person=uid&page=2|                                        |
|   &pagesize=10                             |                                        |
|                                            |                                        |
|                [Cache Miss]                |                                        |
|                                            |--------------------------------------->|
|                                            |   Query next set of comments based    |
|                                            |   on cursor                           |
|                                            |<---------------------------------------|
|                                            |   Return next set of comments and     |
|                                            |   update cursor                       |
|                                            |                                        |
|<-------------------------------------------|                                        |
| Return next set of comments                |                                        |
|                                            |                                        |
|                [Cache Hit]                 |                                        |
|<-------------------------------------------|                                        |
| Return next set of comments                |                                        |
|                                            |                                        |
|                                            |                                        |
|  Cache periodically prefetches and         |                                        |
|  updates based on cursor                   |                                        |
|------------------------------------------->|                                        |
|                                            |                                        |
|                                            |                                        |
|                                            | Database queries are reduced due to    |
|                                            | caching                               |
-----------------------------------------------------------------------------------------------

```

**TALK ABOUT TRADEOFFS FOR EVERY SINGLE COMPONENT INCLUDING THE DATABASES**

**21. Trade-offs & Optimizations (Round 2)**  

### **21. Trade-offs & Optimizations (Round 2)**

When discussing **trade-offs and optimizations**, ensure you cover the following aspects for each component in the system:

#### **1. Performance vs. Scalability**
   - **Explanation**: A component might prioritize performance (low latency, fast response times) at the expense of scalability (ability to handle increasing load). Consider where the system might need to handle **more traffic** or **more users** and decide whether to optimize for **speed or capacity**.
     - **Trade-off Example**: Caching data locally improves performance by reducing database access but increases memory usage and the risk of serving stale data.

#### **2. Latency vs. Consistency**
   - **Explanation**: Systems often need to balance between **low latency** (fast response times) and **data consistency** (correct and synchronized data across all users or systems). Some components may need to favor consistency at the expense of slower responses, while others might prioritize real-time responsiveness.
     - **Trade-off Example**: In chat systems, consistency (delivering messages in the correct order) might conflict with the need for low-latency, real-time messaging.

#### **3. Availability vs. Durability**
   - **Explanation**: Ensuring high availability (system uptime) might involve compromises in **durability** (the guarantee that data is written to storage and won’t be lost). If availability is more critical than durability, the system might tolerate brief data loss or eventual consistency.
     - **Trade-off Example**: A highly available caching system (e.g., Redis) may lose data if it crashes, but the overall system remains operational.

#### **4. Strong vs. Eventual Consistency (CAP Theorem)**
   - **Explanation**: The **CAP theorem** forces a choice between **Consistency, Availability, and Partition Tolerance** in distributed systems. When partitioning happens, either strong consistency (immediate updates across all nodes) or availability (system always responds) must be sacrificed.
     - **Trade-off Example**: Choosing **eventual consistency** for non-critical data (e.g., presence status) ensures higher availability, but the data might be outdated for short periods.

#### **5. Complexity vs. Maintainability**
   - **Explanation**: More **complex systems** (such as using microservices) offer greater flexibility and scalability, but they are harder to maintain and debug. Simple systems (monolithic) are easier to manage but can’t scale as efficiently.
     - **Trade-off Example**: Using microservices increases the need for service orchestration (Kubernetes), logging, and monitoring, making the system harder to maintain, especially in the early stages.

#### **6. Cost vs. Performance**
   - **Explanation**: Systems can be optimized for performance at the cost of increased infrastructure expenses. You need to evaluate how much performance is necessary for the user experience and where cost savings are acceptable.
     - **Trade-off Example**: A CDN reduces media latency but comes at an additional cost. Consider using CDNs selectively to optimize both performance and cost.

#### **7. Memory Usage vs. CPU Usage**
   - **Explanation**: Certain optimizations use more **memory** (RAM) to reduce **CPU computation** or vice versa. An optimized memory cache might reduce the need for expensive CPU computations but increases memory usage.
     - **Trade-off Example**: Storing frequently used data in-memory (Redis) reduces CPU processing time by avoiding repetitive database queries but consumes more RAM.

#### **8. Horizontal vs. Vertical Scaling**
   - **Explanation**: **Horizontal scaling** adds more servers to distribute load, while **vertical scaling** adds more resources (e.g., CPU, memory) to a single machine. Horizontal scaling provides better redundancy but adds complexity.
     - **Trade-off Example**: Horizontally scaling application servers can handle larger loads but increases the complexity of state management (sessions).

#### **9. Caching vs. Fresh Data**
   - **Explanation**: Caching data improves response times and reduces database load, but it can lead to **stale data** if not updated in real time. Consider which parts of the system can tolerate cached data and for how long.
     - **Trade-off Example**: Cache message data to optimize read performance but use time-based expiration or background refresh mechanisms to prevent stale messages.

#### **10. Security vs. Usability**
   - **Explanation**: Increasing security (e.g., strong encryption, frequent authentication checks) can decrease usability by slowing down processes or requiring more user interaction. Systems must balance security needs with user convenience.
     - **Trade-off Example**: End-to-end encryption is critical for messaging privacy, but it can increase message processing time and may limit features like message search.

---

### **Instructions for Applying Trade-offs to Each System Component:**

When designing a system (e.g., for a chat app like WhatsApp or Teams), always evaluate **trade-offs** for each major component or service. This includes **databases, caches, load balancers, application servers, media storage, and more**. Below are examples of how to address trade-offs in different parts of your architecture:

#### **User Clients:**
   - **Latency vs. Data Freshness**: Cache recent chat history locally for faster loading but implement background synchronization to keep it fresh.
   - **Trade-off Optimization**: Use **local storage** for quick access but refresh in the background, ensuring stale data is replaced over time.

#### **API Gateway:**
   - **Security vs. Latency**: Authentication checks (e.g., token verification) can add delay, but caching validated tokens for a short period can reduce redundant checks.
   - **Trade-off Optimization**: Implement token caching for verified requests, reducing latency while ensuring security remains strong.

#### **Application Servers:**
   - **Statelessness vs. Session Management**: Stateless servers are easier to scale but require constant access to the session store.
   - **Trade-off Optimization**: Use **Redis** for session management, allowing the application servers to remain stateless while keeping session data accessible.

#### **Pub/Sub System:**
   - **Consistency vs. Latency**: A system like Kafka offers strong consistency guarantees but at the cost of increased latency.
   - **Trade-off Optimization**: Use **Redis Pub/Sub** for real-time messages (lower consistency, lower latency) and **Kafka** for critical data that requires strong durability and eventual consistency.

#### **Cache (Redis):**
   - **Memory Usage vs. Cache Hit Rate**: Storing more data increases the memory footprint, but also improves the cache hit ratio.
   - **Trade-off Optimization**: Implement **LRU (Least Recently Used)** eviction policies to balance memory usage and ensure high cache efficiency.

#### **Database (Cassandra and PostgreSQL):**
   - **Consistency vs. Availability**: Cassandra can be configured for eventual consistency, which improves availability but may cause temporary inconsistencies.
   - **Trade-off Optimization**: Use **QUORUM** consistency for important operations (like message writes) and **ONE** consistency for non-critical reads (e.g., presence status).

#### **Media Storage (AWS S3):**
   - **Latency vs. Cost**: Using a CDN improves media access speed but increases operational costs.
   - **Trade-off Optimization**: Only serve frequently accessed media via CDN while delivering less popular content directly from S3.

---

### **Data Structures and Trade-offs for the Database**

For each data structure in your system, **clarify what will be stored and why** it’s essential for the performance of the application. The choice of data structure will directly affect **performance, consistency, and scalability**.

---

#### **1. Message Table (Cassandra)**
   - **Stored Data**: `chat_id (Partition Key)`, `message_id (Clustering Key)`, `sender_id`, `content`, `timestamp`, `message_status`, `media_url`.
   - **Why**: Optimized for high write and read performance. Partitioning by `chat_id` ensures all messages for a chat are on the same node, improving the speed of message retrieval.
   - **Trade-off**: Eventual consistency for message data vs. availability; use `QUORUM` for critical chat sessions.

#### **2. User Profile Table (PostgreSQL)**
   - **Stored Data**: `user_id (Primary Key)`, `name`, `email`, `phone_number`, `profile_picture_url`, `status_message`, `last_seen_timestamp`.
   - **Why**: Relational data needs strong consistency and transactional integrity (e.g., updating a profile). PostgreSQL is ideal for this, allowing complex queries on user relationships.
   - **Trade-off**: Strong consistency vs. the complexity of relational management; this is essential for user profiles to avoid data integrity issues.

#### **3. Group Metadata Table (PostgreSQL)**
   - **Stored Data**: `group_id (Primary Key)`, `group_name`, `admin_ids (Array)`, `participant_ids (Array)`, `created_at`, `updated_at`.
   - **Why**: PostgreSQL is ideal for managing relationships between entities, such as groups and participants. Each group’s metadata needs to be strongly consistent to ensure accurate group membership information.
   - **Trade-off**: Relational databases offer strong consistency at the cost of reduced performance under heavy loads; critical for group membership integrity.

#### **4. Media Storage Metadata (Cassandra)**
   - **Stored Data**: `media_id (Partition Key)`, `uploader_id`, `chat_id`, `media_type`, `media_url`, `timestamp`.
   - **Why**: Cassandra handles high write throughput and allows for eventual consistency. This is perfect for metadata linking media files to their users and chats.
   - **Trade-off**: Lower latency and high availability vs. eventual consistency for media uploads.

#### **5

. Redis Cache (For Presence and Recent Messages)**
   - **Stored Data**: Key-value pairs (`user_id → presence_status` and `chat_id → recent_messages`).
   - **Why**: Redis stores frequently accessed data such as user presence and recent messages in-memory, allowing for real-time updates with low latency.
   - **Trade-off**: Increased memory usage vs. fast access to data.

---

### **Summing up Trade-offs and Optimization Approach**

When designing systems, every component faces a trade-off between **performance**, **scalability**, **availability**, **cost**, and **complexity**. The goal is to balance these trade-offs by carefully considering the specific needs of the system. Evaluate each aspect, such as whether **real-time performance** is more important than **data consistency** or if **cost savings** can be achieved without affecting the **user experience**.





