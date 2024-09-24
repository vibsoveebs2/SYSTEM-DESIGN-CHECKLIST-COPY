# SYSTEM DESIGN CHECKLIST

### Comprehensive System Design Prompt Checklist (with API Design)

**1. Functional Requirements**  
- What are the primary features of the system?  
- What are the specific user interactions?

**2. Non-Functional Requirements**  
- What scale should the system handle?  
- What are the key performance metrics?  
- What are the system’s reliability, privacy, and security needs?

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

### **3. API Request Throughput**

- **1 million users/day**, 5 API calls each.
- **138 requests/second** at peak.

**Impact**: **Rate Limiting** at **API Gateway** and auto-scaling **Application Servers**.

---

### **4. Consistency & Availability**

- Eventual consistency for metadata, **multi-region replication** for critical data.

**Impact**: Emphasize **Database** sharding and replication in the design.

---

### **5. Basic API Design**

- **Endpoints**: List the core API endpoints and their functionality.
- **Request/Response**: Define the structure of the requests and responses.
- **Authentication**: How will user authentication and authorization be handled?
- **Versioning**: Will the API be versioned?
- **Rate Limiting**: How will the API handle rate limiting to protect against abuse?

**Sample API Endpoints**:

- **POST /status/update**:
  - **Functionality**: Updates the presence status of a user.
  - **Example Function**: `updateStatus(userId, status)`
  - **Description**: This function will update a user's online/offline status based on the provided activity data.

- **GET /status/{userId}**:
  - **Functionality**: Retrieves the current presence status for a specific user.
  - **Example Function**: `getStatus(userId)`
  - **Description**: Fetches the status of a user from the in-memory store or cache.

- **DELETE /status/{userId}**:
  - **Functionality**: Removes or cleans up the presence status of a user if they have been inactive for an extended period.
  - **Example Function**: `deleteStatus(userId)`
  - **Description**: Handles the cleanup mechanism by purging old records from the system.

- **POST /status/subscribe**:
  - **Functionality**: Allows a client to subscribe to status updates for a list of users.
  - **Example Function**: `subscribeToStatus(userId, subscriberId)`
  - **Description**: Registers subscribers who want updates on a particular user's presence status.

**Additional API Endpoints for Video Processing (e.g., YouTube)**:

- **POST /videos/upload**:
  - **Functionality**: Uploads a video to the platform.
  - **Example Function**: `uploadVideo(userId, videoFile)`
  - **Description**: Handles the initial upload of the video file, splitting it into chunks for upload efficiency.

- **GET /videos/{videoId}/stream**:
  - **Functionality**: Streams a video to the client.
  - **Example Function**: `streamVideo(videoId, format, resolution)`
  - **Description**: Streams video content in chunks, supporting adaptive bitrate streaming.

- **GET /videos/search**:
  - **Functionality**: Searches for videos based on query parameters.
  - **Example Function**: `searchVideos(query, page, pageSize)`
  - **Description**: Uses prefix indexing or inverted indexes for fast search results.

- **POST /videos/{videoId}/process**:
  - **Functionality**: Triggers processing of an uploaded video into different formats and encodings.
  - **Example Function**: `processVideo(videoId)`
  - **Description**: Converts the uploaded video into various formats and resolutions for compatibility and streaming efficiency.

---

### **6. Database Design**

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

      - **Choice**: We will use **`user_id`** or **`video_id`** as the shard key to distribute data across nodes.

      - **Reasoning**:

        1. **Access Patterns Alignment**:
           - **User-Centric Operations**: Many operations involve user-specific data—user profiles, watch history, personalized recommendations.
           - **Data Locality**: Storing user-related data together reduces cross-shard queries for user-centric actions.

        2. **Uniform Data Distribution**:
           - **High Cardinality of `user_id`/`video_id`**: A large and growing number of users and videos ensures even data distribution across shards.
           - **Balanced Load**: Read and write operations are spread out, preventing any single shard from becoming a hotspot.

    - **How the System Interacts with the Database**:
      - Walk through how the system performs reads and writes, optimizations for indexing, and how it ensures high performance during large-scale operations.

        3. **Scalability**:
           - **Horizontal Scaling**: As the user base and content library grow, new shards can be added to handle increased load.
           - **Dynamic Scaling**: Consistent hashing minimizes data movement when scaling out.

        4. **Simplified Maintenance**:
           - **Data Management**: User and video data can be managed, backed up, and restored independently.
           - **Isolation**: Issues affecting one user's data do not impact others.

        5. **Challenges and Mitigations**:
           - **Cross-Shard Queries for Global Operations**:
             - **Challenge**: Operations like generating a global feed or trending videos may require data from multiple shards.
             - **Mitigation**:
               - **Denormalization**: Store aggregated data or indices in centralized or replicated databases.
               - **Secondary Indexes**: Use a search service like Elasticsearch to index content metadata across shards.

---

### **7. Data Structures for Storage and Indexing**

#### **B-Trees and B+ Trees**:
   - **Usage**: Used in **SQL databases** for indexing columns like `user_id`, `video_id`, `title`.
   - **Benefit**: Efficient search, insertion, and deletion operations.
   - **Example**: Indexing `video_id` in a SQL database ensures fast retrieval of video metadata for streaming.

#### **Tries (Prefix Trees)**:
   - **Usage**: Indexing **search terms**, tags, and supporting **autocomplete** features.
   - **Benefit**: Quick retrieval of entries based on prefixes.
   - **Example**: A user types "funny cat" to search for videos. The trie structure quickly narrows down the results by prefix.

#### **Inverted Indexes**:
   - **Usage**: Implemented in **search engines** like **Elasticsearch** for **full-text search**.
   - **Benefit**: Maps words to documents, enabling efficient search queries.
   - **Example**: Searching for videos containing "tutorial" retrieves all relevant videos.

#### **LSM Trees (Log-Structured Merge Trees)**:
   - **Usage**: Used in **NoSQL databases** like **Cassandra** for **write-heavy workloads**.
   - **Benefit**: High write throughput by sequentially writing data to disk.
   - **Example**: Storing comments or likes, which are write-intensive operations.

#### **Bloom Filters**:
   - **Usage**: To quickly check for the **existence of an item**, reducing unnecessary database lookups.
   - **Benefit**: Saves I/O operations by avoiding disk access for non-existent keys.
   - **Example**: Before checking the database for a video's metadata, use a Bloom filter to confirm its existence.

---

### **Optimization of Read and Write Operations**

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

---

### **13. Plaintext Architecture Drawing**

Below is a detailed plaintext architecture diagram, illustrating the system components, redundancy, and data flow.

```
                                +----------------------+
                                |      Load Balancer   |
                                +----------------------+
                                           |
                              +------------+-----------+
                              |                        |
                     +-------------------+   +-------------------+
                     |    API Servers    |   |    API Servers    |   (Redundant)
                     +-------------------+   +-------------------+
                              |                        |
                              |                        |
                      +-------+-------+        +-------+-------+
                      |               |        |               |
             +----------------+  +----------------+  +----------------+
             | Authentication |  |   Video Upload |  |   Video Stream |
             |     Service    |  |    Service     |  |     Service    |
             +----------------+  +----------------+  +----------------+
                      |               |                     |
                      |               |                     |
          +-----------+               |                     |
          |                           |                     |
+----------------+           +--------+-------+     +-------+-------+
| User Database  |           | Video Processing|     | Content Delivery|
|   (SQL/NoSQL)  |           |     Service     |     |    Network     |
+----------------+           +-----------------+     +---------------+
    (Master-Slave)                (Redundant)            (CDN, Edge Servers)
       (Sharded)                                              (Redundant)
          |                           |                     |
          |                           |                     |
          |                   +-------+-------+             |
          |                   |               |             |
          |           +----------------+ +----------------+ |
          |           | Encoding Service| | Thumbnail Gen | |
          |           +----------------+ +----------------+ |
          |                   |               |             |
          |                   |               |             |
          |            +------+-------+ +------+-------+     |
          |            |              | |              |     |
          |     +--------------+ +--------------+ +--------------+
          |     | Video Storage| | Image Storage| | Metadata Store|
          |     |   (Blob)     | |    (Blob)    | |   (NoSQL)     |
          |     +--------------+ +--------------+ +--------------+
          |            (Redundant, Replicated Storage)           |
          |                                                      |
          +------------------------------------------------------+
```



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


**Components and Redundancy**:

- **Load Balancer**: Distributes incoming traffic to API servers. **Redundant** to prevent a single point of failure.
  
- **API Servers**: Stateless servers handling API requests. Scaled horizontally and **redundant**.

- **Authentication Service**: Validates user credentials and tokens. **Redundant** for high availability.

- **Video Upload Service**: Handles video upload requests. Splits videos into chunks for efficient upload. **Redundant**.

- **Video Stream Service**: Streams video content to users. Supports adaptive bitrate streaming. **Redundant**.

- **User Database**: Stores user profiles, credentials. **Sharded and replicated** (Master-Slave or Master-Master) for scalability and redundancy.

- **Video Processing Service**: Processes uploaded videos into different formats and encodings. **Redundant** workers process jobs from a queue.

- **Encoding Service**: Encodes videos into various formats (e.g., MP4, WebM) and resolutions (e.g., 1080p, 720p).

- **Thumbnail Generation**: Generates thumbnails for videos.

- **Video Storage (Blob Storage)**: Stores video files. **Redundant storage** solutions like AWS S3 or Google Cloud Storage.

- **Image Storage (Blob Storage)**: Stores thumbnails and images.

- **Metadata Store (NoSQL)**: Stores video metadata, likes, comments. Uses databases like Cassandra or MongoDB.

- **Content Delivery Network (CDN)**: Delivers video and image content to users efficiently. **Edge servers located globally for low latency**.

---

### **How the System Works (Updated with Core Logic for Video Processing)**

1. **Video Upload Process**:

   - **Client Side**:
     - The user initiates a video upload via the client application.
     - The video file is **split into chunks** (e.g., 5MB each) to allow resumable uploads and efficient network utilization.

   - **API Server**:
     - Receives the upload request and authenticates the user.
     - Provides **pre-signed URLs** or upload tokens for secure direct upload to Blob Storage.

   - **Blob Storage**:
     - The client uploads video chunks directly to Blob Storage using the provided URLs.

   - **Video Upload Service**:
     - Once the upload is complete, the service triggers the **Video Processing Service**, passing the video location.

2. **Video Processing**:

   - **Video Processing Service**:
     - Pulls the video from Blob Storage.
     - **Transcodes the video into multiple formats** (e.g., H.264, VP9) and **resolutions** (e.g., 1080p, 720p, 480p).
     - **Splits the processed video into streaming chunks** (e.g., HLS or DASH segments).

   - **Thumbnail Generation**:
     - Extracts key frames to create thumbnails.
     - Stores thumbnails in Image Storage.

   - **Metadata Update**:
     - Updates the **Metadata Store** with video information, including available formats, resolutions, thumbnail locations.

3. **Video Streaming**:

   - **Client Side**:
     - User requests to watch a video.
     - The client selects the appropriate video quality based on network conditions (**adaptive streaming**).

   - **API Server**:
     - Authenticates the request.
     - Retrieves video metadata from the Metadata Store.

   - **Content Delivery Network (CDN)**:
     - Delivers video chunks to the client.
     - **Edge servers cache content** for faster delivery.

4. **Search Functionality**:

   - **Client Side**:
     - User enters search terms.

   - **API Server**:
     - Receives the search query.
     - Uses a **Search Service** (e.g., Elasticsearch) with **inverted indexes** to perform fast full-text search.

   - **Search Service**:
     - Queries the inverted index for matching videos.
     - Supports **prefix indexing** for autocomplete suggestions.

   - **Response**:
     - Returns search results to the client.

---

### **21. Trade-offs & Optimizations**

When designing each component, we must consider the trade-offs between various factors such as performance, scalability, consistency, and complexity.

#### **1. Load Balancer**

- **Trade-offs**:
  - **Performance vs. Complexity**: Implementing advanced routing rules can improve performance but adds complexity.
  - **Optimization**: Use simple round-robin or least connections algorithms for load distribution.

#### **2. API Servers**

- **Trade-offs**:
  - **Statelessness vs. Session Management**: Keeping servers stateless allows easy scaling but requires external session storage.
  - **Optimization**: Use tokens (e.g., JWT) to avoid server-side session storage.

#### **3. Authentication Service**

- **Trade-offs**:
  - **Security vs. Latency**: Strong encryption and frequent token validation enhance security but may introduce latency.
  - **Optimization**: Cache authentication tokens with short TTLs to reduce validation overhead.

#### **4. Video Upload Service**

- **Trade-offs**:
  - **Upload Speed vs. Reliability**: Larger chunks can speed up uploads but are more susceptible to failure.
  - **Optimization**: Use moderate chunk sizes and support resumable uploads.

#### **5. Video Processing Service**

- **Trade-offs**:
  - **Processing Speed vs. Resource Utilization**: Allocating more resources speeds up processing but increases cost.
  - **Optimization**: Implement autoscaling for processing nodes based on workload.

#### **6. Video Storage (Blob Storage)**

- **Trade-offs**:
  - **Cost vs. Performance**: Premium storage options offer better performance at a higher cost.
  - **Optimization**: Use standard storage tiers for older or less frequently accessed videos.

#### **7. Metadata Store (NoSQL Database)**

- **Trade-offs**:
  - **Consistency vs. Availability**: Strong consistency ensures up-to-date data but may reduce availability.
  - **Optimization**: Use eventual consistency where possible, e.g., for view counts, and strong consistency for critical data.

#### **8. Content Delivery Network (CDN)**

- **Trade-offs**:
  - **Cost vs. Latency**: Wider CDN coverage reduces latency but increases cost.
  - **Optimization**: Cache popular content at edge locations and use geo-routing to serve users from the nearest server.

#### **9. Search Service**

- **Trade-offs**:
  - **Indexing Speed vs. Search Performance**: Frequent updates to the index may slow down indexing but keep search results fresh.
  - **Optimization**: Batch index updates during off-peak hours for non-critical data.

#### **10. Caching**

- **Trade-offs**:
  - **Memory Usage vs. Cache Hit Rate**: Caching more data improves hit rate but consumes more memory.
  - **Optimization**: Use eviction policies (e.g., LRU) and set appropriate TTLs.

#### **11. Sharding Strategy**

- **Trade-offs**:
  - **Data Locality vs. Load Balancing**: Sharding by `user_id` may lead to uneven load if some users are more active.
  - **Optimization**: Use consistent hashing and consider rebalancing strategies.

#### **12. Data Consistency**

- **Trade-offs**:
  - **Strong Consistency vs. Latency**: Ensuring immediate consistency can increase response times.
  - **Optimization**: Use eventual consistency where acceptable and implement read-your-own-writes for user-specific data.

#### **13. Video Encoding Formats**

- **Trade-offs**:
  - **Quality vs. Bandwidth**: Higher quality videos require more bandwidth.
  - **Optimization**: Offer multiple resolutions and allow clients to select based on their network conditions.

#### **14. Network Bandwidth**

- **Trade-offs**:
  - **Throughput vs. Cost**: Higher bandwidth improves performance but increases costs.
  - **Optimization**: Compress data and use efficient protocols (e.g., HTTP/2, QUIC).

#### **15. Security**

- **Trade-offs**:
  - **Security vs. Usability**: Strict security measures may hinder user experience.
  - **Optimization**: Implement user-friendly authentication mechanisms and monitor for anomalies.

#### **16. Logging and Monitoring**

- **Trade-offs**:
  - **Comprehensiveness vs. Performance**: Detailed logs provide better insights but may affect performance.
  - **Optimization**: Use asynchronous logging and sampling.

#### **17. Microservices vs. Monolith**

- **Trade-offs**:
  - **Complexity vs. Flexibility**: Microservices offer modularity but add complexity.
  - **Optimization**: Start with a modular monolith and refactor into microservices as needed.

#### **18. Database Choices**

- **Trade-offs**:
  - **Relational vs. NoSQL**: SQL databases offer ACID transactions; NoSQL databases provide scalability.
  - **Optimization**: Use polyglot persistence, choosing the right database for each use case.

---

### **Summing up Trade-offs and Optimization Approach**

Balancing trade-offs is critical in system design. Each component must be evaluated based on:

- **Performance**: How fast does it need to be?
- **Scalability**: Can it handle growing loads?
- **Consistency**: How critical is data accuracy?
- **Availability**: Must it always be available?
- **Cost**: What is the budget?

By carefully considering these factors, we can design a system that meets the core requirements while optimizing for efficiency and user experience.

---

This updated checklist incorporates detailed diagrams, API endpoints, core logic (such as video processing and streaming), and trade-offs for each component, ensuring a comprehensive system design that addresses both functional and non-functional requirements.