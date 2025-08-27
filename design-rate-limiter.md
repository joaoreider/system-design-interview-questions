
### 1. Clarify functional requirements
Getting a picture of the high level that we're trying to accomplish and discuss some important details.

- For what are we designing a rate limiter?
- A Backend API? A single API or multiple microservices in a reusable way?
- How to identify clients? uuid? IP? *(IP is the most common because not necessary the client will have a uuid, for instance there an unauthenticated page that he is requesting)*


### 2. Non-functional requirements
More about things like performance, how well it should performed.