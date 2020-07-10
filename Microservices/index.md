# Microservices

## Monolithic Server v/s Microservices

Monolith contains and implements all the features of our app, while a single micro service implements one services in our application.

## Data in Microservices

Data management between services is extremely difficult to solve. Services have their own database and there is no cross talk. We want every service to run independently. This architecture is also called Database per service

_Data management between services is the biggest challenge_

## Why Microservices

1. Avoid single points of failure
2. Some services work better for specific types of databases
3. Schema changes might affect entire application

## Table Of Contents

1. [[Communication Strategies between services]]
2. [[Mini microservices application]]