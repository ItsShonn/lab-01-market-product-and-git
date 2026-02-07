## Product Choice

-Product name: Telegram

-Link: [Telegram](https://telegram.org/)

-Short description: Telegram is a cloud-based instant messaging service with a focus on speed and security. It offers end-to-end encrypted chats, large group capabilities, and file sharing up to 2GB.

## Main components

![Telegram Component Diagram](../../../docs/diagrams/out/telegram/component-diagram/Component%20Diagram.svg)
![telegram component diagram code](../../../docs/diagrams/src/telegram/component-diagram.puml)

Selected components:

-MTProto Protocol Handler: Handles Telegram's proprietary encryption protocol, securing all communications between clients and servers.

-Message Queue Service: Manages real-time message delivery and ensures messages reach recipients even when they're offline.

-Media Storage Service: Stores and serves user-uploaded media files with deduplication to optimize storage usage.

-Session Manager: Maintains user session states and handles authentication across multiple devices simultaneously.

-Update Service: Broadcasts real-time updates (online status, typing indicators, message edits) to connected clients.

## Data flow

![Telegram Sequence Diagram](../../../docs/diagrams/out/telegram/sequence-diagram/Sequence%20Diagram.svg)
![telegram sequence diagram code](../../../docs/diagrams/src/telegram/sequence-diagram.puml)
Selected action group: "Process and Deliver Message"

This group handles the complete flow of sending and receiving a text message. The Mobile Client first sends the encrypted message to the API Gateway via MTProto. The Message Service validates and stores the message, then the Update Service notifies the recipient's client. If the recipient is offline, the Message Queue Service stores the message for later delivery. Finally, the recipient's client acknowledges receipt, completing the delivery confirmation loop.

Components involved:

-Mobile Client → API Gateway: Encrypted message data using MTProto

-API Gateway → Message Service: Validated message payload

-Message Service → Update Service: Message metadata for notification

-Update Service → Mobile Client: Real-time push notification

-Message Queue Service → Message Service: Offline message storage/retrieval

## Deployment

![Telegram Deployment Diagram](../../../docs/diagrams/out/telegram/deployment-diagram/Deployment%20Diagram.svg)
![telegram deployment diagram code](../../../docs/diagrams/src/telegram/deployment-diagram.puml)
Deployment description:
Telegram employs a globally distributed architecture with data centers strategically located worldwide. The API Gateways and Message Services are deployed in multiple regions to minimize latency. Media Storage uses dedicated clusters with CDN integration for fast file delivery. Database services are replicated across regions for redundancy, while the MTProto handlers are deployed as separate security-focused clusters.

## Assumptions

-Media deduplication: I assume Telegram implements content-based deduplication in its Media Storage Service, where identical files uploaded by different users are stored only once with reference counting.

-Session synchronization: I assume the Session Manager uses a distributed cache system (like Redis) to maintain real-time session states across multiple data centers.

-Protocol optimization: I assume MTProto Protocol Handler includes connection pooling and session reuse to reduce handshake overhead for frequent communications.

-Queue persistence: I assume the Message Queue Service uses disk-backed queues with memory caching to ensure message durability even during system failures.

## Open questions

-Secret chat implementation: How does Telegram implement the peer-to-peer encryption in secret chats while still providing cloud backup options for regular chats?

-Load balancing strategy: What specific load balancing algorithms does Telegram use to distribute traffic across its global data centers, especially during DDoS attacks?

-Database sharding: How does Telegram shard its user data across multiple database instances while maintaining fast cross-user interactions (like group chats)?

-Cache invalidation: What cache invalidation strategy does Telegram use for frequently updated data like online status and typing indicators?
