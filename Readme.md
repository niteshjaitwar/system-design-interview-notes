
# [System Design Interview - An Insider's Guide (Vol 1)](https://bytebytego.com/courses/system-design-interview)
These notes are based on the System Design Interview books - [Vol 1 2nd Ed](https://www.goodreads.com/book/show/54109255-system-design-interview-an-insider-s-guide) 

This repository is a structured collection of:
- system design foundations from networking basics to distributed systems
- system design interview notes
- distributed systems concepts and trade-offs
- chapter-wise architecture case studies
- a Java 8 to 26 feature tracker

It is useful for:
- interview preparation
- backend and distributed systems revision
- quick refreshers before system design rounds
- Java language and platform evolution review

**Note:** These notes are a work in progress.

## What Is Inside
- Foundational networking and system design basics such as DNS, IP, ports, HTTP/HTTPS, TLS, caches, replication, sharding, and queues
- Foundational topics such as scaling, estimation, and design frameworks
- Distributed systems building blocks such as rate limiting, consistent hashing, and key-value stores
- End-to-end design problems such as chat systems, web crawlers, notification systems, YouTube, and Google Drive
- A release-by-release Java tracker from Java 8 through JDK 26


 * [Chapter 0 - Foundations Of System Design](./00.%20Foundations%20of%20System%20Design/)
 * [Chapter 1 - Scale From Zero To Millions Of Users](./01.%20Scaling/)
 * [Chapter 2 - Back-of-the-envelope Estimation](./02.%20Back%20Of%20the%20Envelope%20Estimation/)
 * [Chapter 3 - A Framework For System Design Interviews](./03.%20System%20Design%20Framework/)
 * [Chapter 4 - Design A Rate Limiter](./04.%20Rate%20Limiter//)
 * [Chapter 5 - Design Consistent Hashing](./05.%20Consistent%20Hashing/)
 * [Chapter 6 - Design A Key-Value Store](./06.%20Key-Value%20Store/)
 * [Chapter 7 - Design A Unique ID Generator In Distributed Systems](./07.%20Unique-Id%20Generator/)
 * [Chapter 8 - Design A URL Shortener](./08.%20URL%20Shortener/)
 * [Chapter 9 - Design A Web Crawler](./09.%20Web%20Crawler/)
 * [Chapter 10 - Design A Notification System](./10.%20Notification%20System/)
 * [Chapter 11 - Design A News Feed System](./11.%20News%20Feed%20System/)
 * [Chapter 12 - Design A Chat System](./12.%20Chat%20System/)
 * [Chapter 13 - Design A Search Autocomplete System](./13.%20Search%20Autocomplete/)
 * [Chapter 14 - Design YouTube](./14.%20Youtube/)
 * [Chapter 15 - Design Google Drive](./15.%20Google%20Drive/)
 * [Chapter 16 - Proximity Service](./16.%20Proximity%20Service/)
 * [Chapter 17 - Java 8 To 26 Feature Tracker](./17.%20Java%208%20To%2026%20Feature%20Tracker/)


## Suggested GitHub Subtitle
`System design foundations, interview notes, distributed systems concepts, architecture case studies, and Java 8 to 26 feature tracking.`

## Suggested GitHub Description
`Structured system design foundations and interview notes with distributed systems fundamentals, architecture case studies, and a Java 8 to 26 feature tracker.`

## Suggested GitHub Topics
`system-design`, `distributed-systems`, `networking`, `scalability`, `interview-prep`, `backend`, `java`, `software-architecture`, `notes`

## Suggested Reading Order
1. Start with Foundations of System Design, then read Scaling, Estimation, and the System Design Framework
2. Move to core infrastructure topics such as Rate Limiter, Consistent Hashing, and Key-Value Store
3. Study end-to-end design case studies such as URL Shortener, Chat System, YouTube, and Google Drive
4. Use the Java tracker as a separate language and platform revision guide

## Study Roadmap
### 30-Day Plan
1. Days 1-5: Read [Chapter 0](./00.%20Foundations%20of%20System%20Design/), [Chapter 1](./01.%20Scaling/), and [Chapter 2](./02.%20Back%20Of%20the%20Envelope%20Estimation/). Focus on networking, request flow, scaling vocabulary, and estimation basics.
2. Days 6-8: Read [Chapter 3](./03.%20System%20Design%20Framework/). Practice turning vague product ideas into structured interview discussions.
3. Days 9-15: Study [Chapter 4](./04.%20Rate%20Limiter/), [Chapter 5](./05.%20Consistent%20Hashing/), [Chapter 6](./06.%20Key-Value%20Store/), and [Chapter 7](./07.%20Unique-Id%20Generator/). These are the core infrastructure building blocks.
4. Days 16-20: Study [Chapter 8](./08.%20URL%20Shortener/), [Chapter 9](./09.%20Web%20Crawler/), [Chapter 10](./10.%20Notification%20System/), and [Chapter 11](./11.%20News%20Feed%20System/). Practice explaining read path, write path, and trade-offs.
5. Days 21-26: Study [Chapter 12](./12.%20Chat%20System/), [Chapter 13](./13.%20Search%20Autocomplete/), and [Chapter 16](./16.%20Proximity%20Service/). Focus on low latency, indexing, and real-time behavior.
6. Days 27-30: Study [Chapter 14](./14.%20Youtube/) and [Chapter 15](./15.%20Google%20Drive/). Review all exercises and do timed mock interview answers.

### 60-Day Plan
1. Weeks 1-2: Build strong fundamentals with Chapters 0-3.
2. Weeks 3-4: Master infrastructure topics with Chapters 4-7.
3. Weeks 5-6: Cover medium-complexity product designs with Chapters 8-11.
4. Weeks 7-8: Cover advanced real-time and storage-heavy designs with Chapters 12-16.
5. Final days: Revisit weak chapters, redo exercises, and practice whiteboard-style explanations without notes.

### How to Use the Notes
- Read once for vocabulary and high-level architecture.
- Read again to identify bottlenecks, trade-offs, and failure modes.
- Answer the interview questions and end-of-chapter exercises without looking at the notes.
- Practice explaining one write path and one read path for every design chapter.

## Fast Revision
- [System Design Interview Handbook](./SYSTEM_DESIGN_INTERVIEW_HANDBOOK.md)


# Additonal Resources

### Rate Limiting
- [Circuit Breaker Algorithm](https://martinfowler.com/bliki/CircuitBreaker.html)
- [Uber Rate Limiter](https://github.com/uber-go/ratelimit/blob/master/ratelimit.go)


### Consistent Hashing
- [Consistent Hashing](https://tom-e-white.com/2007/11/consistent-hashing.html)
- [CS168: Introduction and Consistent Hashing:]( http://theory.stanford.edu/~tim/s16/l/l1.pdf)
- [Apache Cassandra](http://www.cs.cornell.edu/Projects/ladis2009/papers/Lakshman-ladis2009.PDF)
- [Scaling Discord](https://blog.discord.com/scaling-elixir-f9b8e1e7c29b)
- [Google Maglev](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/44824.pdf)


### Key-Value Store
- [Amazon Dynamo](https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf)
- [Cassandra Architecture](https://docs.datastax.com/en/archived/cassandra/3.0/cassandra/architecture/archIntro.html)
- [Google BigTable Architecture](https://static.googleusercontent.com/media/research.google.com/en//archive/bigtable-osdi06.pdf)
- [Amazon Dynamo DB Internals](https://www.allthingsdistributed.com/2007/10/amazons_dynamo.html)
- [Design Patterns in Amazon Dynamo DB](https://www.youtube.com/watch?v=HaEPXoXVf2k)
- [Internals of Amazon Dynamo DB](https://www.youtube.com/watch?v=yvBR71D0nAQ)


### Unique-ID Generator
- [Ticket Servers: Distributed Unique Primary Keys on the Cheap](https://code.flickr.net/2010/02/08/ticket-servers-distributed-unique-primary-keys-on-the-cheap)
- [Snowflake](https://blog.twitter.com/engineering/en_us/a/2010/announcing-snowflake.html)


### Web Crawler
- [Web Crawling](http://infolab.stanford.edu/~olston/publications/crawling_survey.pdf)
- [Google Dynamic Rendering](https://developers.google.com/search/docs/guides/dynamic-rendering)



### Chat Systems
- [How Discord stores billions of messages](https://discord.com/blog/how-discord-stores-billions-of-messages)
- [Flannel: An Application-Level Edge Cache to Make Slack Scale](https://slack.engineering/flannel-an-application-level-edge-cache-to-make-slack-scale/)


### Search Autocomplete
- [How We Built Prefixy](https://medium.com/@prefixyteam/how-we-built-prefixy-a-scalable-prefix-search-service-for-powering-autocomplete-c20f98e2eff1)
- [Prefix Hash Tree](https://people.eecs.berkeley.edu/~sylvia/papers/pht.pdf)


### Youtube
- [YouTube Architecture](http://highscalability.com/youtube-architecture)
- [YouTube scalability 2012](https://www.youtube.com/watch?v=w5WVu624fY8)
- [Transcoding Videos at Scale](https://www.egnyte.com/blog/2018/12/transcoding-how-we-serve-videos-at-scale/)
- [Facebook Video Broadcasting](https://engineering.fb.com/ios/under-the-hood-broadcasting-live-video-to-millions/)
- [Netflix Video Encoding at Scale](https://netflixtechblog.com/high-quality-video-encoding-at-scale-d159db052746)
- [Netflix Shot based encoding](https://netflixtechblog.com/optimized-shot-based-encodes-now-streaming-4b9464204830)


### Google Drive
- [Differential Synchronization](https://neil.fraser.name/writing/sync/)
- [Differential Synchronization Video](https://www.youtube.com/watch?v=S2Hp_1jqpY8)
- [How We’ve Scaled Dropbox](https://www.youtube.com/watch?v=PE4gwstWhmc&feature=youtu.be)
