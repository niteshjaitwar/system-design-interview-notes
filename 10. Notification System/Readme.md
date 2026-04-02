# Chapter 10: Design a Notification System

## Introduction
A **notification system** is essential for modern applications, providing timely updates like product notifications, events, offers, and alerts. Notifications can be sent through:
1. **Push notifications** (mobile or desktop),
2. **SMS messages**, and
3. **Emails**.

The chapter focuses on designing a scalable system capable of sending millions of notifications daily.

---

## Step 1: Understanding the Problem
### Requirements
- **Notification Types:** Push notifications, SMS, and Emails.
- **Delivery:** Soft real-time system with minimal delays.
- **Platforms:** iOS, Android, and desktop.
- **Triggers:** Notifications can be triggered by client applications or scheduled on servers.
- **Scale:**
  - **Push Notifications:** 10 million/day,
  - **SMS:** 1 million/day,
  - **Emails:** 5 million/day.
- **Opt-out Support:** Users can disable specific notification types.

---

## Step 2: High-Level Design

### Components

1. **Notification Types:**
   - **iOS Push Notifications:** Use **Apple Push Notification Service (APNS)**.
   - **Android Push Notifications:** Use **Firebase Cloud Messaging (FCM)**.
   - **SMS Messages:** Third-party services like Twilio or Nexmo.
   - **Emails:** Commercial email services like SendGrid or Mailchimp.

2. **Contact Info Gathering:**
   <div style="margin-left:3rem">
      <img src="./images/contact-info-gathering.png" alt="Contact Info Gathering" width="500">
   </div>

   - Collect device tokens, phone numbers, or email addresses during app installation or signup.
   - Store contact info in the database:
     - **Device Tokens Table:** For push notifications.
     - **User Table:** For emails and phone numbers.


3. **Notification Sending Flow:**

   <div style="margin-left:3rem">
      <img src="./images/high-level-design.png" alt="High Level Design" width="500">
   </div>

   - **Trigger Services:**
      - Generate events to initiate notifications (e.g., billing reminders, shipping updates).
      - A service can be a micro-service, a cron job, or a distributed system that triggers notification sending events.
   - **Notification Server:** 
      - Provide APIs for services to send notifications. 
      - Carry out basic validations to verify emails, phone numbers.
      - Query the database or cache to fetch data needed to render a notification.
   - **Third-Party Services:** Deliver notifications to users.

     

### Challenges in Initial Design
- **Single Point of Failure (SPOF):** One notification server can crash the entire system.
- **Scalability Issues:** Hard to scale databases, caches, and processing components independently.
- **Performance Bottlenecks:** High resource demands for sending notifications.

### Improved Design

   <div style="margin-left:3rem">
      <img src="./images/improved-design.png" alt="Improved Design" width="500">
   </div>

- Move databases and caches out of the notification server.
- Introduce **horizontal scaling** with multiple notification servers.
- Use **message queues** to decouple system components.
   -  Message queues serve as buffers when high volumes of notifications are to be sent out.
- Add workers that pull notification events from message queues and send them to corresponding third party services.

   

---

## Step 3: Design Deep Dive

### Reliability
1. **Prevent Data Loss:** 
   <div style="margin-left:3rem">
   <img src="./images/data-loss.png" alt="Data Loss" width="400">
   </div>

   - Persist notification data in a database and implement a retry mechanism. 
   - The Notification log database is included for data persistence.


2. **Deduplication:** 
   - Check event IDs to avoid sending duplicate notifications.
   - When a notification event first arrives, check if it is seen before by checking the event ID.
If seen before discard it, otherwise send out the notification. 


### Additional Components
   <div style="margin-left:3rem">
   <img src="./images/events-tracking.png" alt="Events Tracking" width="400">
   </div>

1. **Notification Templates:** Preformatted templates for consistent and efficient notifications.
2. **Notification Settings:**
   - Users can opt-in or opt-out for specific channels (push, SMS, or email).
   - Stored in a dedicated notification settings table.
3. **Rate Limiting:** Cap the frequency of notifications sent to users.
4. **Retry Mechanism:** Retry sending notifications if third-party services fail.
5. **Monitoring Queues:** Track queued notifications to scale workers dynamically.
6. **Event Tracking:** Collect metrics like open rate, click rate, and engagement.


### Security
- Use **AppKey** and **AppSecret** to authenticate and secure APIs for push notifications.

### Notification Flow

   <div style="margin-left:3rem">
   <img src="./images/updated-design.png" alt="Updated Design" width="500">
   </div>

1. Trigger services call APIs to send notifications.
2. Notification servers validate requests and fetch metadata from caches or databases.
3. Notification events are sent to message queues.
4. Workers process events and interact with third-party services.
5. Third-party services deliver notifications to users.


---

## Key Optimizations
1. **Horizontal Scaling:** Add more notification servers for load distribution.
2. **Message Queues:** Decouple processing to handle high volumes.
3. **Caching:** Reduce latency by caching frequently accessed data.
4. **Distributed Crawling:** Optimize message delivery geographically for better performance.

---

## Beginner Notes
### What a Notification System Usually Includes
- email
- SMS
- push notification
- in-app notification

### Why This System Needs Decoupling
Notification sending is usually asynchronous because delivery depends on:
- external providers
- retries
- templates
- user preferences

## Advanced Design Questions
- How do you deduplicate repeated events?
- How do you avoid sending duplicate notifications across retries?
- How do you respect provider rate limits?
- How do you prioritize critical alerts over promotional messages?

## Common Mistakes
- Ignoring user preference management.
- Ignoring delivery receipts and bounce handling.
- Coupling business logic directly to provider APIs.

---

## Interview Questions
1. Why should notification delivery usually be asynchronous?
2. How do you model notification templates and localization?
3. How do retries avoid duplicate user-visible alerts?
4. How do you handle provider outages or slowdowns?
5. What data should be stored for auditing and delivery tracking?

## Chapter Glossary
- **Provider**: external service used to deliver email, SMS, or push.
- **Bounce**: email or SMS delivery failure returned by a provider.
- **DLQ**: dead-letter queue for repeatedly failing events.
- **Preference center**: user-configurable settings for notification channels and frequency.

---

## Example Walkthrough
### Example: Sending a Password Reset Email
1. User requests password reset.
2. Application emits a notification event.
3. Event enters a queue.
4. Worker loads the email template and user contact information.
5. Worker calls the email provider API.
6. Delivery status is stored for auditing and retries.

## Exercises
1. Why should password reset notifications usually be high priority?
2. Why are queues useful between application logic and providers?
3. What should happen after repeated provider failures?

---

## One-Minute Revision
- notifications are usually asynchronous
- providers can fail or rate-limit
- queues decouple traffic spikes
- preferences and retries are first-class concerns
- auditability matters

## Exercise Answers
1. Password reset messages are user-blocking and often security-critical, so delay directly hurts the user.
2. Queues buffer traffic, isolate provider slowness, and make retries easier to manage.
3. Events should move to retry workflows or a DLQ and trigger alerts or failover to another provider.
