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

The core requirement of the system is to handle and fulfill specific user functionality (e.g., real-time video uploading, processing, streaming, and user interactions like comments and subscriptions). The service must efficiently manage these operations and ensure proper system behavior under load.

#### **Out-of-the-Box Solutions**:

1. **Core Functionality**: Define how the system will achieve its primary goal (e.g., uploading videos, processing them into different formats, splitting them into chunks for streaming, and handling user interactions like comments and subscriptions).

2. **Data Processing/Storage**: Define any regular operations (e.g., cleanup, batch processes) required to maintain the system's health, such as data purging or asynchronous operations to ensure performance is maintained.

---

### **Data Sharding & Memory Storage**

The system will often need to be distributed across multiple nodes or servers. Here's how data sharding and memory management should work:

1. **Sharding Logic**:  
   - Each user or entity is assigned to a specific shard based on a defined shard key (e.g., `user_id`, `video_id`). 
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
   - Describe the logic for handling incoming updates, such as video uploads, processing requests, and how they are processed (e.g., calculating the shard for the entity and updating the necessary in-memory structures).

2. **Storage Redundancy**:
   - Define how the system maintains redundancy for high availability (e.g., replicating data across multiple servers or shards).

3. **Cleanup or Maintenance Process**:
   - Define any cleanup jobs that run periodically (e.g., removing inactive data or purging old subscriptions). Also, explain how data is written to the database asynchronously to ensure performance.

4. **Subscription Management**:
   - Explain how subscriptions are managed and updated. This includes appending connections to subscription lists and sending updates to subscribers when necessary.

---

### **Walkthrough of Core Requirement**

1. **Entity/Request Flow**:
   - Walk through the flow of the core feature (e.g., how a video is processed from when it is uploaded until it is available for streaming).

2. **Event Notification**:
   - Explain how the system notifies relevant subscribers or systems when an event happens, such as a new video upload or live streaming start.

3. **Handling Inactivity**:
   - Define how the system handles inactivity or similar events, including any background processes that handle cleanup.

4. **Subscription Notification Flow**:
   - Detail how subscribers are notified when an event (e.g., new video upload, live stream) occurs for the entity they are subscribed to.

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

- **POST /videos/upload**:
  - **Functionality**: Initiates the upload of a video to the platform.
  - **Example Function**: `uploadVideo(userId, videoMetadata)`
  - **Description**: Handles the initial upload of the video file, splitting it into chunks for upload efficiency.

- **GET /videos/{videoId}/stream**:
  - **Functionality**: Streams a video to the client.
  - **Example Function**: `streamVideo(videoId, format, resolution)`
  - **Description**: Streams video content in chunks, supporting adaptive bitrate streaming.

- **POST /videos/{videoId}/process**:
  - **Functionality**: Triggers processing of an uploaded video into different formats and encodings.
  - **Example Function**: `processVideo(videoId)`
  - **Description**: Converts the uploaded video into various formats and resolutions for compatibility and streaming efficiency.

- **GET /videos/search**:
  - **Functionality**: Searches for videos based on query parameters.
  - **Example Function**: `searchVideos(query, page, pageSize)`
  - **Description**: Uses prefix indexing or inverted indexes for fast search results.

- **POST /comments/{videoId}**:
  - **Functionality**: Adds a comment to a video.
  - **Example Function**: `addComment(videoId, userId, commentText)`
  - **Description**: Handles user comments on videos.

- **GET /comments/{videoId}**:
  - **Functionality**: Retrieves comments for a video.
  - **Example Function**: `getComments(videoId, page, pageSize)`
  - **Description**: Paginates through comments for a video.

---

### **6. Database Design**

- **Database Types**:

  - **Relational Database (SQL)**:
    - **Usage**: For structured data like user accounts and video metadata.
    - **Examples**: PostgreSQL, MySQL.

  - **NoSQL Database**:
    - **Usage**: For scalable storage of comments, likes, and activity logs.
    - **Examples**: Cassandra, MongoDB.

  - **In-Memory Database**:
    - **Usage**: Use Redis for caching and session management.

- **Sharding and Partitioning**:

  - **Horizontal Sharding**:

    - **Shard Key Selection**:

      - **Choice**: Use **`user_id`** or **`video_id`** as the shard key to distribute data across nodes.

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
  - **Redis Cache**: Frequently accessed metadata (e.g., user profiles, video metadata) is stored in Redis for fast access.
  - **CDN**: Use for serving static content such as images and videos, reducing the load on blob storage.
  - **Example**: Redis is used to cache frequently requested **user profiles** in a video streaming app.

- **Database Indexing**:
  - **Indexes on Key Fields**: Create indexes on frequently queried fields like `user_id`, `video_id`.
  - **Example**: Indexing `video_id` in the database for faster retrieval of video data.

#### **Write Optimization**:

- **Batch Processing**:
  - **Aggregation of logs**: Write log entries in batches to reduce the number of disk writes.
  - **Example**: A logging system that batches user activity logs and writes them to disk at regular intervals.

- **Log-Based Storage Engines**:
  - **LSM Trees**: Store comments or interactions with sequential writes.
  - **Example**: Handling high write throughput in the comments system.

---

### **13. Plaintext Architecture Drawing**

Below is a detailed plaintext architecture diagram, illustrating the system components, redundancy, and data flow. This diagram incorporates detailed components, showing redundancy and sharding strategies.

```
                                    +----------------------+
                                    |         Users        |
                                    +----------+-----------+
                                               |
                                               v
                                     +---------+----------+
                                     |     Load Balancer   |
                                     +----------+----------+
                                               |
                               +---------------+---------------+
                               |                               |
                               v                               v
                     +------------------------+     +------------------------+
                     | S3 Storage (Primary)   |     | S3 Storage (Backup)    |
                     +-----------+------------+     +------------+-----------+
                                 |                                |
                                 +-------------+------------------+
                                               |
                                               v
                                     +---------+----------+
                                     |   Upload Service    |
                                     |      (Primary)      |
                                     +----------+----------+
                                               |
                                               v
                                     +---------+----------+
                                     |   Upload Service    |
                                     |      (Backup)       |
                                     +----------+----------+
                                               |
                                               v
                                +--------------+--------------+
                                |                             |
                                v                             v
                  +-------------------------+   +-------------------------+
                  |   Video Chunks (Raw)    |   |     Video Metadata      |
                  | Stored in S3 (Primary)  |   | Kafka (Shard on VideoID)|
                  | Stored in S3 (Backup)   |   +-----------+-------------+
                  +-------------------------+               |
                                                            v
                                                   +--------+--------+
                                                   |       Flink      |
                                                   | Shard on VideoID |
                                                   |     (Primary)    |
                                                   +--------+--------+
                                                            |
                                                            v
                                                   +--------+--------+
                                                   |       Flink      |
                                                   | Shard on VideoID |
                                                   |     (Backup)     |
                                                   +--------+--------+
                                                            |
                                                            v
                                                   +------------------+
                                                   |  Chunk Processors |
                                                   +--------+---------+
                                                            |
                                                            v
                                                   +--------+--------+
                                                   |   Chunk Completer |
                                                   | Kafka (Shard on   |
                                                   |     UserID)       |
                                                   |     (Primary)     |
                                                   +--------+--------+
                                                            |
                                                            v
                                                   +--------+--------+
                                                   |   Chunk Completer |
                                                   | Kafka (Shard on   |
                                                   |     UserID)       |
                                                   |     (Backup)      |
                                                   +--------+--------+
                                                            |
                                                            v
                                                   +--------+--------+
                                                   |       Flink      |
                                                   | Shard on UserID  |
                                                   |     (Primary)    |
                                                   +--------+--------+
                                                            |
                                                            v
                                                   +-------------------+
                                                   | Users/Videos Table |
                                                   |       (MySQL)      |
                                                   | Shard on UserID    |
                                                   |     (Primary)      |
                                                   +-------------------+
                                                            |
                                                            v
                                                   +-------------------+
                                                   | Users/Videos Table |
                                                   |       (MySQL)      |
                                                   | Shard on UserID    |
                                                   |     (Backup)       |
                                                   +-------------------+

                             +------------------------------------------+
                             |    Subscriber Table (MySQL)              |
                             | Shard on UserID (Primary + Backup)       |
                             +-------------------------+----------------+
                                                            |
                                                            v
                                                   +-------------------+
                                                   |  Elasticsearch     |
                                                   |     (Primary)      |
                                                   +---------+---------+
                                                             |
                                                             v
                                                   +---------+---------+
                                                   |  Elasticsearch     |
                                                   |     (Backup)       |
                                                   +-------------------+

                             +------------------------------------------+
                             |  Comment Service (Primary + Backup)      |
                             +-------------------------+----------------+
                                                            |
                                                            v
                                                   +--------+--------+
                                                   |     Load Balancer  |
                                                   +--------+--------+
                                                            |
                                                            v
                                                   +-------------------+
                                                   |   Comments Table   |
                                                   |    (Cassandra)     |
                                                   | Shard on ChannelID,|
                                                   |     VideoID        |
                                                   | Sort on Timestamp  |
                                                   |     (Primary)      |
                                                   +-------------------+
                                                            |
                                                            v
                                                   +-------------------+
                                                   |   Comments Table   |
                                                   |    (Cassandra)     |
                                                   | Shard on ChannelID,|
                                                   |     VideoID        |
                                                   | Sort on Timestamp  |
                                                   |     (Backup)       |
                                                   +-------------------+

                             +--------------------------+
                             | Video Chunks Table (MySQL)|
                             | Shard on UserID, VideoID |
                             |   (Primary + Backup)     |
                             +------------+-------------+
                                          |
                                          v
                                   +------+------+
                                   |     CDN     |
                                   |  (Primary)  |
                                   +------+------+
                                          |
                                          v
                                   +------+------+
                                   |     CDN     |
                                   |  (Backup)   |
                                   +-------------+
```

**Explanation of Components and Redundancy**:

- **Users**: Clients interacting with the system.

- **Load Balancer (LB)**: Distributes incoming traffic to various services. **Redundant** for high availability.

- **S3 Storage (Primary and Backup)**: Stores raw video chunks uploaded by users. Data is replicated for redundancy.

- **Upload Service (Primary and Backup)**: Handles uploading of video chunks. **Redundant** for fault tolerance.

- **Video Chunks (Raw)**: The raw uploaded video chunks stored in S3 storage.

- **Video Metadata (Kafka, Sharded on VideoID)**: Metadata about videos is streamed via Kafka topics partitioned by `video_id`.

- **Flink (Primary and Backup, Sharded on VideoID)**: Processes streaming data for video processing tasks.

- **Chunk Processors**: Responsible for processing video chunks, such as encoding and transcoding.

- **Chunk Completer (Kafka, Sharded on UserID)**: Manages the completion of video chunk processing.

- **Flink (Primary and Backup, Sharded on UserID)**: Processes data streams related to user-specific tasks.

- **Users/Videos Table (MySQL, Sharded on UserID)**: Stores information about users and their videos. Sharded and replicated for scalability and redundancy.

- **Subscriber Table (MySQL, Sharded on UserID)**: Manages user subscriptions. Data is sharded and replicated.

- **Elasticsearch (Primary and Backup)**: Used for search functionality, indexing video metadata for quick retrieval.

- **Comment Service (Primary and Backup)**: Handles comments on videos, ensuring high availability.

- **Comments Table (Cassandra, Sharded on ChannelID, VideoID)**: Stores comments, optimized for high write throughput and scalability. Replicated for redundancy.

- **Video Chunks Table (MySQL, Sharded on UserID, VideoID)**: Tracks video chunks and their processing status.

- **CDN (Primary and Backup)**: Content Delivery Network for distributing video content to users efficiently.

**Redundancy and High Availability**:

- **Primary and Backup Services**: Critical services have both primary and backup instances to prevent single points of failure.

- **Sharding**: Data is partitioned based on keys like `user_id` and `video_id` to distribute load and improve performance.

- **Replication**: Databases and data stores are replicated to ensure data durability and availability in case of failures.

---

### **How the System Works (Updated with Core Logic for Video Processing)**

1. **Video Upload Process**:

   - **Client Side**:
     - The user initiates a video upload via the client application.
     - The video file is **split into chunks** (e.g., 5MB each) to allow resumable uploads and efficient network utilization.

   - **Upload Service**:
     - Receives the upload request and authenticates the user.
     - Provides **pre-signed URLs** or upload tokens for secure direct upload to S3 Storage.
     - Manages uploads to both **Primary** and **Backup** S3 storage for redundancy.

2. **Video Processing**:

   - **Kafka (Video Metadata)**:
     - Video metadata is published to Kafka topics, sharded on `video_id`.

   - **Flink (Processing Video Metadata)**:
     - Consumes video metadata from Kafka.
     - Coordinates with **Chunk Processors** to process video chunks.

   - **Chunk Processors**:
     - Pulls raw video chunks from S3 Storage.
     - **Processes video chunks** (e.g., encoding into different formats and resolutions).
     - Stores processed chunks back into S3 Storage.

   - **Chunk Completer (Kafka)**:
     - After processing, messages are sent to Kafka topics sharded on `user_id` to indicate completion.

   - **Flink (User-Specific Processing)**:
     - Monitors chunk completion messages.
     - Updates the **Users/Videos Table** in MySQL to reflect the video's processed status.

3. **Data Storage and Indexing**:

   - **Users/Videos Table (MySQL)**:
     - Stores user information and video metadata.
     - Sharded on `user_id` for scalability.

   - **Subscriber Table (MySQL)**:
     - Manages subscriptions and follower relationships.
     - Sharded and replicated for high availability.

   - **Elasticsearch**:
     - Indexes video metadata for efficient search capabilities.

4. **Content Delivery**:

   - **CDN**:
     - Distributes processed video content to users.
     - Caches content from S3 Storage for fast delivery.

5. **Comments and User Interaction**:

   - **Comment Service**:
     - Handles incoming comments on videos.
     - Interacts with **Comments Table** in Cassandra.

   - **Comments Table (Cassandra)**:
     - Stores comments, sharded on `channel_id` and `video_id`, and sorted by `timestamp`.

6. **Redundancy and Failover**:

   - **Primary and Backup Instances**:
     - All critical components have backup instances.
     - Automatic failover mechanisms ensure high availability.

---

### **21. Trade-offs & Optimizations**

When designing each component, we must consider the trade-offs between various factors such as performance, scalability, consistency, and complexity.

#### **1. Load Balancer**

- **Trade-offs**:
  - **Performance vs. Complexity**: Advanced routing improves performance but adds complexity.
- **Optimization**:
  - Use simple algorithms like round-robin for load distribution to balance performance and simplicity.

#### **2. Upload Service**

- **Trade-offs**:
  - **Upload Speed vs. Reliability**: Larger chunks speed up uploads but risk data loss on failure.
- **Optimization**:
  - Choose optimal chunk sizes (e.g., 5MB) and support resumable uploads.

#### **3. Storage (S3 Primary and Backup)**

- **Trade-offs**:
  - **Cost vs. Durability**: Storing data in multiple locations increases cost but improves durability.
- **Optimization**:
  - Use lifecycle policies to transition older data to cheaper storage tiers.

#### **4. Data Processing (Flink, Kafka, Chunk Processors)**

- **Trade-offs**:
  - **Throughput vs. Latency**: Processing data in real-time reduces latency but may reduce throughput.
- **Optimization**:
  - Balance real-time processing with batch operations where appropriate.

#### **5. Databases (MySQL, Cassandra, Elasticsearch)**

- **Trade-offs**:
  - **Consistency vs. Availability**: Strong consistency ensures data accuracy but may reduce availability.
- **Optimization**:
  - Use eventual consistency for non-critical data and strong consistency for critical user data.

#### **6. CDN**

- **Trade-offs**:
  - **Coverage vs. Cost**: Wider CDN coverage reduces latency but increases costs.
- **Optimization**:
  - Cache popular content and use regional CDNs to balance performance and cost.

#### **7. Comments System**

- **Trade-offs**:
  - **Write Scalability vs. Read Performance**: Cassandra is optimized for high write throughput.
- **Optimization**:
  - Use appropriate data modeling in Cassandra to optimize read paths.

#### **8. Search Functionality (Elasticsearch)**

- **Trade-offs**:
  - **Indexing Speed vs. Query Performance**: Frequent indexing updates can slow down search performance.
- **Optimization**:
  - Schedule indexing during off-peak hours and use incremental indexing.

#### **9. Redundancy**

- **Trade-offs**:
  - **Availability vs. Cost**: More redundancy improves availability but increases infrastructure costs.
- **Optimization**:
  - Implement redundancy for critical components and evaluate the necessity for less critical ones.

#### **10. Sharding Strategy**

- **Trade-offs**:
  - **Complexity vs. Scalability**: Sharding increases scalability but adds complexity to data management.
- **Optimization**:
  - Use consistent hashing and automated shard management tools.

---

### **Summing up Trade-offs and Optimization Approach**

Balancing trade-offs is critical in system design. Each component must be evaluated based on:

- **Performance**: Ensuring the system meets latency and throughput requirements.
- **Scalability**: Designing for growth in users and data volume.
- **Consistency**: Determining where strong consistency is necessary versus eventual consistency.
- **Availability**: Ensuring high uptime and fault tolerance.
- **Cost**: Managing infrastructure and operational expenses.

By carefully considering these factors, we can design a system that meets the core requirements while optimizing for efficiency and user experience.

---

This updated checklist incorporates the detailed diagram provided, ensuring it is neatly integrated into the system design. It includes comprehensive explanations of each component, their redundancy, and the trade-offs involved in designing a scalable and efficient video streaming platform.