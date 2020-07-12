# Event Syncing

It is not essential that all our events are always in sync, so it is important to find a way to sync up our application in case of:
1. Additional services being added later
2. A service being down for sometime during the lifespan of the running application
   
We can handle this in a couple of ways, namely:
## Sync Requests :x:

``` mermaid
   sequenceDiagram
    participant Post Service
    participant Comment Service
    participant Query Service
    Post Service -->> Query Service: Give me all the posts
    Comment Service -->> Query Service: Give me all the comments
   ```
Disadvantages
1. Falling back to synchronous requests.
2. Need bulk endpoints for services (getting data over entire lifetime).

## Direct Database Access :x:
``` mermaid
   sequenceDiagram
    participant Post Database
    participant Comment Database
    participant Query Service
    Post Database -->> Query Service: Direct access to all the posts
    Comment Database -->> Query Service: Direct access to all the comments
   ```

Disadvantages
1. Fallback to synchronous requests
2. Need to interact with all databases, leading to extra code
   
## Store Events :white_check_mark:

``` mermaid
   sequenceDiagram
    participant Post Service
    participant Comment Service
    participant Query Service
    participant Event Bus Service
    Event Bus Service -->> Query Service: Query all events that have occurred so far, in order
   ```
Disadvantages
1. Can have millions of events
   
### This can also handle dropped events