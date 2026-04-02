# Chapter 13: Design a Search Autocomplete System

## Introduction
Autocomplete, also known as typeahead or incremental search, provides real-time suggestions to users as they type in search boxes. The system must efficiently deliver top-k relevant and popular suggestions based on historical query data.

### Key Features
- Suggest up to **5 autocomplete results**.
- Based on **query popularity** (frequency).
- Support only **lowercase English characters**.
- Fast response time (<100 ms) and scalable.

---

## Step 1: Understanding the Problem

### Requirements
1. **Real-Time Suggestions:** Display relevant matches as the user types.
2. **Top-k Results:** Return up to 5 results sorted by popularity.
3. **Scalability:** Handle **10 million DAU** with a peak QPS of **48,000**.
4. **High Availability:** Handle failures without system downtime.
5. **Data Growth:** Support daily storage growth of **0.4 GB** for new query data.

---

## Step 2: High-Level Design
At the high-level, the system is broken down into two services:
1. **Data Gathering Service:** 
    - Collects user queries and aggregates them for frequency analysis in real-time.
    - Real-time processing is not practical for large data sets; however, it is a good starting point


2. **Query Service:** Provides the top-k suggestions based on the user’s input.

---

### Data Gathering Service
<div style="margin-left:3rem">
    <img src="./images/data-gathering.png" alt="Data Gathering" width="600">
</div>

- Aggregates query data from analytics logs and updates the frequency table.
- Processes historical data weekly to build a **trie** (prefix tree).




### Query Service
<div style="margin-left:3rem">
    <img src="./images/frequency-table.png" alt="Frequency Table" width="400">
    <img src="./images/basic-search-suggestions.png" alt="Search Suggestions" width="360">
</div>

- Uses the frequency table from data gathering service.
- Processes user input and retrieves top-k suggestions from the frequency table using a Trie.
- Optimized for fast lookups using caching and efficient data structures.
- For example when a user types “tw” in the search box, the following top 5 searched queries are displayed.


---

## Step 3: Design Deep Dive

### Trie Data Structure
The **trie** is a tree-like data structure used to store and retrieve query strings efficiently.

#### Key Features
1. **Compact Storage:** Represents prefixes hierarchically to minimize redundancy.
2. **Frequency Information:** Stores the popularity of queries at each node.

4. **Steps to get top k most searched queries**
   <div style="margin-left:3rem">
      <img src="./images/trie-structure.png" alt="Trie Structure" width="500">
   </div>

    - Find the prefix
    - Traverse the subtree from prefix node to get all valid children
    - Sort the children and get top k 


3. **Optimizations:**
   - Cache top-k queries at each node to speed up retrieval and avoid traversing the whole trie.

        <img src="./images/cached-trie.png" alt="Cached Trie" width="600">

   - Limit prefix length to reduce search space as users rarely type a loong search query (say 50).

#### Trie Operations
1. **Create:** 
    - Built weekly using aggregated query data.
    - The source of data is from Analytics Log/DB.
2. **Update:** Rarely updated in real-time; weekly updates replace old data.
3. **Delete:** 
      <div style="margin-left:3rem">
         <img src="./images/delete-kv.png" alt="Delete KV" width="500">
      </div>

    - Filters remove unwanted or harmful suggestions (e.g., hate speech).
    - Having a filter layer gives us the flexibility of removing results based on different filter rules.
    - Unwanted suggestions are removed physically from the database asynchronically.
    

---

### Query Processing Flow
1. **Prefix Search:**
   - Identify the prefix node corresponding to the user’s input.
   - Traverse the subtree to collect valid suggestions.
2. **Top-k Sorting:**
   - Cache top-k suggestions at each node to minimize sorting overhead.
3. **Response Construction:**
   - Construct results using cached data for fast response times.

---

### Optimizations
1. **Cache at Each Node:**
   - Store the top-k queries to avoid redundant traversals.
2. **Limit Prefix Length:**
   - Cap prefix length to a small value (e.g., 50 characters) for faster lookups.
3. **AJAX Requests:**
   - Use lightweight asynchronous requests for real-time responses.
4. **Browser Caching:**
   - Save autocomplete results in the browser cache for frequently searched terms.

---

### Data Gathering Pipeline
In the high-level design, whenever a user types a search query, data is updated in real-time. This appraoch is not practical.
- Users may enter billions of queries per day. Updating the trie on every query is not feasible.
- Top suggestions may not change much one the trie is built.


#### Updated Design

<div style="margin-left:3rem">
   <img src="./images/data-gathering-flow.png" alt="Updated Data Gathering Flow" width="600">
</div>

1. **Analytics Logs:**
   - Stores raw query data as logs for weekly aggregation.
   - Logs are append-only and are not indexed
2. **Aggregators:**
   - Process logs into frequency tables, suitable for trie construction.
   - For real-time applications such as Twitter, aggregate data in a shorter time interval.
   - For other cases, aggregating data less frequently, say once per week is good enough.
3. **Workers:**
   - Asynchronous servers rebuild the trie and store it in persistent storage.
4. **Storage Options:**
    - **Trie Cache**: Trie Cache is a distributed cache system that keeps trie in memory for fast read.
    - **Trie DB** 
        1. **Document Store (e.g., MongoDB)**: Since a new trie is built weekly, we can periodically take a snapshot of it, serialize it, and store the serialized data in the database like MongoDB
        2. **Key-Value Store:** 
            - Maps prefixes to node data for fast access.
            - Every prefix in the trie is mapped to a key in a hash table.
            - Data on each trie node is mapped to a value in a hash table.

                <img src="./images/trie-db.png" alt="Trie DB" width="600">
---

### Scalability
1. **Sharding:**
   - Distribute trie nodes across servers based on prefix ranges (e.g., `a-m`, `n-z`).
   - Further shard within prefixes to balance uneven distributions (e.g., `aa-ag`, `ah-an`).
2. **Load Balancing:**
   <div style="margin-left:3rem">
      <img src="./images/sharding.png" alt="Sharding" width="400">
   </div>

   - Use a shard map manager to route requests to the appropriate server.


---

## Step 4: Advanced Features

### Multi-Language Support
1. **Unicode Characters:** Use Unicode to support non-English languages.
2. **Country-Specific Tries:** Build separate tries for different countries or regions.

### Trending Queries
- Handle real-time events by dynamically updating trie nodes or weighting recent queries more heavily.

---

## Beginner Notes
### What Autocomplete Optimizes For
- low latency
- relevant suggestions
- high cache hit rate
- frequent updates to hot queries

### Why a Trie Is Popular
A trie groups strings by prefix, making prefix lookups efficient.

## Advanced Design Questions
- Do updates need to be real-time or batch-based?
- How do you handle multilingual tokenization?
- How do you support typo tolerance?
- Should popular prefixes be precomputed and cached?

## Common Mistakes
- Treating autocomplete as full-text search.
- Ignoring ranking freshness.
- Ignoring memory growth of trie-based indexes.

---

## Interview Questions
1. Why is a trie better than a relational table scan for prefix suggestions?
2. How do you keep suggestions fresh without rebuilding everything continuously?
3. How do you rank by frequency and recency together?
4. How do you support typo tolerance or fuzzy matching?
5. What parts should be cached aggressively?

## Chapter Glossary
- **Prefix**: the starting sequence of characters in a query.
- **Trie**: tree-like structure optimized for prefix operations.
- **Top-k**: the highest-ranked `k` suggestions for a prefix.
- **Fuzzy match**: approximate match that tolerates spelling mistakes.

---

## Example Walkthrough
### Example: Returning Suggestions for `ca`
1. User types `c`, then `ca`.
2. The service finds the trie node for prefix `ca`.
3. It returns the top-ranked suggestions stored under that node, such as `car`, `cat`, and `camera`.
4. Results may be filtered by locale, freshness, or personalization.

## Exercises
1. Why does storing top-k suggestions at trie nodes reduce latency?
2. Why are batch updates often simpler than real-time index mutation?
3. What is the difference between autocomplete and full-text search?

---

## One-Minute Revision
- autocomplete is prefix-based and latency-sensitive
- trie is the core structure
- ranking needs freshness and popularity
- top-k caching avoids deep traversal on every keystroke

## Exercise Answers
1. The service can return results directly from the prefix node without scanning the whole subtree at query time.
2. Batch updates are easier to reason about, cheaper operationally, and avoid high write churn on hot prefixes.
3. Autocomplete predicts likely next queries from prefixes, while full-text search finds matching documents across full content.
