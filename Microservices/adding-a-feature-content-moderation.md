# Adding a Feature

We are adding a content moderation feature.
It would ideally involve adding a 'status' property to comment which can be any of 'accepted', 'rejected' or 'processing'.

New features can be added in a variety of ways, namely

1. Only specific services can answer the call to Comment/create. :x:
   ``` mermaid
   sequenceDiagram
    participant Comment Service
    participant Moderation Service
    participant Query Service
    participant Event Bus
    Comment Service->>Event Bus: { type: "Comment created", body: {id: 1, content: 'Hello there!'}}
    Note right of Event Bus: The Event Bus passes this to the Moderation Service
    Event Bus ->> Moderation Service: { type: "Comment created", body: {id: 1, content: 'Hello there!'}}
    Note left of Moderation Service: The Moderation service approved or rejects the comment
    Moderation Service -->> Event Bus: { type: "Comment Moderated", body: {id: 1, content: 'Hello there!', status: 'Approved'}}
    Event Bus -->> Query Service: { type: "Comment Moderated", body: {id: 1, content: 'Hello there!', status: 'Approved'}}
   ```
   A major flaw here is that the comment may not be visible to the user till the moderation is complete

2. Adding a default status to each comment, and events being received by both the Moderation as well as the Comments service instantly :x:
   ``` mermaid
   sequenceDiagram
   participant Comment Service
   participant Moderation Service
   participant Query Service
   participant Event Bus
   Comment Service->>Event Bus: { type: "Comment created", body: {id: 1, content: 'Hello there!'}}
   Note right of Event Bus: The Event Bus passes this to the Moderation Service and the Query Service
   Event Bus ->> Moderation Service: { type: "Comment created", body: {id: 1, content: 'Hello there!'}}
   Event Bus ->> Query Service: { type: "Comment created", body: {id: 1, content: 'Hello there!', status: 'Pending'}}
   Note left of Moderation Service: The Moderation service approved or rejects the comment
   Moderation Service -->> Event Bus: { type: "Comment Moderated", body: {id: 1, content: 'Hello there!', status: 'Approved'}}
   Event Bus -->> Query Service: { type: "Comment Moderated", body: {id: 1, content: 'Hello there!', status: 'Approved'}}
   ```
   This solves the problem of the comment not being visible instantly.

There are still some major drawbacks to these approaches, as here the Query service inspite of simply being a presentation level service does so much query handling. We should ideally want to handle all this prior to reaching the query service.

3. Ensure specialised updates are handled in the emitting service and have our presentation services accept generic services. :white_check_mark:
   ``` mermaid
   sequenceDiagram
    participant Comment Service
    participant Moderation Service
    participant Query Service
    participant Event Bus
    Comment Service->>Event Bus: { type: "Comment created", body: {id: 1, content: 'Hello there!', status: 'Pending'}}
    Note right of Event Bus: The Event Bus passes this to the Moderation Service
    Event Bus ->> Moderation Service: { type: "Comment created", body: {id: 1, content: 'Hello there!', status: 'Pending'}}
    Event Bus ->> Query Service: { type: "Comment created", body: {id: 1, content: 'Hello there!', status: 'Pending'}}
    Note left of Moderation Service: The Moderation service approved or rejects the comment, all the while the Query Service displays pending.
    Moderation Service -->> Event Bus: { type: "Comment Moderated", body: {id: 1, content: 'Hello there!', status: 'Approved'}}
    Event Bus -->> Comment Service: { type: "Comment Updated", body: {id: 1, content: 'Hello there!', status: 'Approved'}}
    Note left of Comment Service: The Comment Service can now handle all kind of comment specific tasks, eg: Comment Upvoted, Comment pinned etc. 
    Event Bus -->> Query Service: { type: "Comment Updated", body: {id: 1, content: 'Hello there!', status: 'Approved'}}
   ```
   Here, delegating all our comment service specific changes (Eg: upvoting, downvoting, pinning etc.) are delegated to within the comment services. 


### Note: Now, we can simulate the 'Pending' status by simply shutting down the Moderation service.

This brings us to the topic of [[Event Syncing]], as after shutting it down, we now have a stale state in our application.