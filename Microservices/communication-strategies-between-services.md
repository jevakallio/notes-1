# Communication Strategies between Services

## Sync :x:

Communicate with each other using direct requests

### **Advantages**

1. Easy
2. No Extra Database

### **Disadvantages**

1. Introduces dependency
2. Only as fast as the slowest request
3. Can introduce a LOT of dependencies internally.

## Async :white_check_mark:

Communicate between each other using events.

### Type 1: Introduce Event bus into the application.

Event Bus - all services communicate with Event bus. (Hence is a single point of failure, requires resilient infrastructure)

Eg event: (Type and data possibly), can ingest events and send them over to different services. This style not the best.

### Type 2 - Refine micro service objective and define a database to solve this

Simultaneously added to event bus and database, now this event can be ingested by any service and keep their respective databases updated.

### **Advantages**

1. No dependencies
2. D will be very fast

### **Disadvantages**

1. Extra Storage