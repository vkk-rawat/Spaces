# Spaces
Create chatroom and chat . Simple 

## A high level architecture of the application.
                           +------------------------------+
                           |  Users (Web / Mobile Clients) |
                           +--------------+---------------+
                                          |
                                          v
+--------------------------------------------------------------+
|               API Gateway (Kong / Nginx)                     |
|       - Handles Authentication (OAuth, JWT)                 |
|       - Load Balancing & Rate Limiting                      |
|       - Routing Requests to Microservices                   |
+--------------------------------------------------------------+
                     |                     |                      |
                     v                     v                      v
     +-----------------------+  +---------------------+  +---------------------+
     |  Authentication Service|  |  Chat Service      |  |  Notification Service|
     |  (OAuth2, JWT, RBAC)   |  |  (WebSockets, gRPC)|  |  (Push, Email, Web) |
     +-----------------------+  +---------------------+  +---------------------+
                     |                     |                      |
                     v                     v                      v
  +------------------------+  +-------------------------+  +--------------------------+
  |  User Service          |  |  Message Queue (Kafka)  |  |  AI Moderation Service    |
  |  (User Profiles, RBAC) |  |  - Message Fan-out      |  |  - Content Filtering      |
  +------------------------+  |  - Event Handling       |  +--------------------------+
                     |        +-------------------------+             |
                     v                  |                              v
  +----------------------------+  +-----------------------+  +-----------------------+
  |  PostgreSQL (User, Meta)   |  |  Cassandra (Messages) |  |  Elasticsearch (Search)|
  |  - User Data               |  |  - High-Volume Chats  |  |  - Search & Indexing   |
  +----------------------------+  +-----------------------+  +-----------------------+
                     |                     |                      |
                     v                     v                      v
           +-------------------+  +------------------+  +------------------+
           | Redis Cache        |  | File Storage    |  | ML Pipelines      |
           | - Presence Tracking|  | - AWS S3 / MinIO|  | - Summarization   |
           +-------------------+  +------------------+  +------------------+


#### Components
--API Gateway (Kong / Nginx)
Centralized entry point for users.
Routes requests to appropriate microservices.
Handles authentication (JWT, OAuth2).
Implements rate limiting and security policies.
-- Authentication Service
Provides OAuth2, JWT, WebAuthn support.
Handles user roles & permissions (RBAC).
Manages session tracking & identity verification.
--Chat Service
Uses WebSockets for real-time messaging.
Stores chat history in Cassandra (NoSQL for fast writes).
Supports threaded chats, group messages.
Publishes messages to Kafka for processing.
--User Service
Manages user profiles, settings, and contacts.
Stores data in PostgreSQL.
Uses Redis for fast lookups.
--Notification Service
Listens to Kafka events for new messages.
Sends push notifications (FCM, APNs, Web).
Supports email & in-app notifications.
--AI Moderation Service
Uses TensorFlow/PyTorch for NLP-based toxicity detection.
Filters offensive messages before they reach users.
Summarizes long conversations with AI.
--Message Queue (Kafka)
Ensures scalability & event-driven processing.
Manages message fan-out for multiple recipients.
--Database Layer
✅ PostgreSQL (User & Metadata)
Stores structured data like user profiles.
✅ Cassandra (Chat Messages)
Handles high-volume chat storage.
✅ Elasticsearch
Provides full-text search on chat messages.
--Caching Layer (Redis)
Tracks user presence (online/offline).
Speeds up chat history retrieval.
--Storage (AWS S3 / MinIO)
Stores profile pictures, chat media (images, audio, videos).
Uses CDN (Cloudflare, AWS CloudFront) for global delivery.