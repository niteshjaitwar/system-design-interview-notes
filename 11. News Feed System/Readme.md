# Chapter 11: Design a News Feed System

## Introduction
A **news feed system** displays a constantly updating list of posts (status updates, photos, videos, and links) from a user’s connections. Examples include Facebook’s news feed, Instagram’s feed, and Twitter’s timeline. This chapter explores the design of a scalable news feed system.

---

## Step 1: Understanding the Problem

### Requirements
1. **Platform:** The system supports both web and mobile apps.
2. **Features:**
   - Users can publish posts.
   - Users can view posts from friends in their news feed.
3. **Sorting:** Feeds are sorted in **reverse chronological order** for simplicity.
4. **Scale:**
   - Users can have up to 5,000 friends.
   - 10 million daily active users (DAU).
   - Feeds may include text, images, and videos.

---

## Step 2: High-Level Design

### Overview
The design includes two main flows:
1. **Feed Publishing:** A user publishes a post, which is written to the database and propagated to their friends’ feeds.
2. **News Feed Building:** A user retrieves their news feed by aggregating posts from friends in reverse chronological order.

---

### News Feed APIs
1. **Feed Publishing API:**
   - **Endpoint:** `POST /v1/me/feed`
   - **Params:** `content` (post text) and `auth_token` (authentication).

2. **News Feed Retrieval API:**
   - **Endpoint:** `GET /v1/me/feed`
   - **Params:** `auth_token` (authentication).

---

### Feed Publishing

   <div style="margin-left:3rem">
      <img src="./images/feed-publishing.png" alt="Feed Publishing" width="400">
   </div>

1. **User Interaction:** The user publishes a post via the feed publishing API.
2. **Load Balancer:** Distributes traffic to web servers.
3. **Web Servers:** Authenticate requests and redirect to services.
4. **Post Service:** Stores the post in the database and cache.
5. **Fanout Service:** Propagates the post to friends’ news feeds in the cache.
6. **Notification Service:** Sends notifications to friends.

---

### News Feed Building

   <div style="margin-left:3rem">
      <img src="./images/news-feed-building.png" alt="News Feed Building" width="400">
   </div>

1. **User Interaction:** The user requests their news feed via the retrieval API.
2. **Load Balancer:** Distributes traffic to web servers.
3. **Web Servers:** Forward requests to the news feed service.
4. **News Feed Service:** Fetches post IDs from the news feed cache and retrieves complete post details from the database or cache.

   
---

## Step 3: Design Deep Dive

### Feed Publishing Deep Dive
1. **Web Servers:**
   - Authenticate users using `auth_token`.
   - Enforce rate limits to prevent spam.

2. **Fanout Service:**
   - **Fanout on Write:** Push posts to friends’ feeds at write time.
     - **Pros:** Real-time updates, fast feed retrieval.
     - **Cons:** Resource-intensive for users with many friends.
   - **Fanout on Read:** Pull posts at read time.
     - **Pros:** Efficient for inactive users.
     - **Cons:** Slower feed retrieval.
   - **Hybrid Approach:** Use a push model for most users and a pull model for high-connection users (e.g., celebrities).

        <img src="./images/feed-publishing-deep-dive.png" alt="Feed Publishing Deep Dive" width="500">

    The **fanout service** works as following:

    1. **Fetch Friend IDs:** Retrieve the friend list from a graph database.
    2. **Filter Friends from Cache:** Access user settings in the cache to exclude certain friends (e.g., muted friends or selective sharing preferences).
    3. **Send to Message Queue:** Send the filtered friend list along with the new post ID to a message queue for processing.
    4. **Fanout Workers:** Workers retrieve data from the message queue and update the news feed cache. The cache stores `<post_id, user_id>` mappings instead of full user and post objects to save memory.
    5. **Store in News Feed Cache:** Append new post IDs to the friends’ news feed cache. A configurable limit ensures that only recent posts are stored, as most users focus on the latest content, keeping cache memory consumption manageable.

        <img src="./images/fanout-service.png" alt="Fanout Service" width="500">

## News Feed Retrieval Deep Dive

### Cache Architecture
The cache is divided into five layers:
1. **News Feed Cache:** Stores post IDs for quick retrieval.
2. **Content Cache:** Stores post details (popular posts in hot cache).
3. **Social Graph Cache:** Stores user relationship data.
4. **Action Cache:** Tracks user actions (likes, replies, shares).
5. **Counter Cache:** Maintains counts for likes, replies, followers, etc.

    <img src="./images/cache-architecture.png" alt="Cache Architecture" width="500">
---

## Key Optimizations

### Scaling
1. **Database Scaling:**
   - Horizontal scaling and sharding.
   - Use of read replicas for high-traffic queries.
2. **Stateless Web Tier:** Keep web servers stateless to enable horizontal scaling.

### Caching
1. Store frequently accessed data in memory.
2. Use cache layers to reduce latency and database load.

### Reliability
1. **Consistent Hashing:** Distribute requests evenly across servers.
2. **Message Queues:** Decouple system components and buffer traffic.

### Monitoring
1. Track key metrics like QPS (queries per second) and latency.
2. Monitor cache hit rates and adjust configurations accordingly.

---

## Beginner Notes
### Two Fundamental Feed Models
- **Fanout on write**: push a post into followers' feed data early.
- **Fanout on read**: build the feed when the user opens it.

### Why Feeds Are Hard
Feeds are simple for normal users but difficult for celebrities because one post may need to reach millions of followers.

## Advanced Design Questions
- Should the feed be chronological or ranked?
- How are celebrity accounts handled?
- How is feed pagination done safely?
- How do you support deletions and privacy changes?

## Common Mistakes
- Using only fanout on write or only fanout on read without discussing hybrids.
- Ignoring ranking and relevance.
- Ignoring cache invalidation when posts are deleted or hidden.

---

## Interview Questions
1. When should you use fanout on write versus fanout on read?
2. How do celebrity users change the architecture?
3. What are the trade-offs between chronological feeds and ranked feeds?
4. How do you paginate feeds without duplicates or missing items?
5. How do you delete content that has already been fanned out widely?

## Chapter Glossary
- **Feed item**: a post reference shown in a user's feed.
- **Fanout**: distributing one write to many downstream recipients.
- **Ranking**: scoring and ordering posts by relevance.
- **Timeline**: ordered set of content associated with a user.

---

## Example Walkthrough
### Example: Fanout on Write for a Normal User
1. User publishes a post.
2. The post metadata is stored.
3. The system fetches follower IDs.
4. The post reference is inserted into each follower's feed storage.
5. When followers open the app, feed retrieval is fast because the feed is already materialized.

## Exercises
1. Why is fanout on write bad for celebrity users?
2. What does a hybrid feed architecture try to combine?
3. Why is pagination harder than just sorting by time?

---

## One-Minute Revision
- feed systems are dominated by read scale
- fanout on write is fast to read
- fanout on read is cheaper for massive follower counts
- ranking and pagination are key complexities

## Exercise Answers
1. A celebrity post may need to be copied to millions of followers, which makes write amplification too high.
2. A hybrid design combines fast reads for normal users with lazy or selective fanout for celebrity-scale accounts.
3. Pagination must avoid duplicates, gaps, and unstable ordering while data keeps changing underneath.
