# Requirements (5 mins):

## Functional Requirements

> Identify core features (e.g., "Users should be able to post tweets"). Prioritize 2-3 key features.

1. users can browse lists of coding problems
2. users can view the problem and coding in different languages 
3. users can submit a solution to the coding problem and get the result
4. users can view the lead board

## Non-Functional Requirements

> Focus on system qualities like scalability, latency, and availability. Quantify where possible (e.g., "render feeds in under 200ms").

1. scale 1m DAU
2. availability >> consistency 
3. security, isolate env to run code
4. user should be able to validate the solution within 1 second

## Capacity Estimation

> Skip unnecessary calculations unless they directly impact the design (e.g., sharding in a TopK system).

100k users take competition, refresh lead board requests 6 per minute,  QPS is 100k x 6 / 60 = 10k, heavy load read

## Core Entities (2 mins)

>  Identify key entities (e.g., User, Tweet, Follow) to define the system's foundation.

- problem
- solution
- lead board

# API/System Interface (5 mins)

> Define the contract between the system and users. Prefer RESTful APIs unless GraphQL is necessary.

- GET /problems?page&company&category -> [problems]
- GET /problem/id -> {desc, category, tags ...}
- POST /solution/problem_id, body {lang, code} -> [pass/fail, timecost]
- GET /leadboard/problem_id?page -> [rankings]


# [Optional] Data Flow (5 mins)

> Describe high-level processes for data-heavy systems (e.g., web crawlers).

# High-Level Design (10-15 mins)

> Draw the system architecture, focusing on core components (e.g., servers, databases). Keep it simple and iterate based on API endpoints.

![image](https://github.com/user-attachments/assets/6e5d88a2-1af9-4a68-a10f-b4378e717842)

# Deep Dives (10 mins)

> Address non-functional requirements, edge cases, and bottlenecks. Proactively improve the design (e.g., scaling, caching, database sharding).

## how to achieve isolation and security

- mount the code as read only, and write any output to /tmp directory
- set limits for CPU/memory usage for the container
- avoid infinite loop or long time run, run as subprocess and monitor timeout, kill if needed
-  limited network access (VPC)
- no system calls, mock it, or restrict it

## how to solve the heavy read load of query lead board

We can use cache, but it won't be up to date, we can use [Redis sorted set](https://redis.io/docs/latest/develop/data-types/sorted-sets/) to implement leadboard, update both Redis and DB, but just query Redis to get the top N.

## how to scale to support 100k concurrent users for competition

The submission is CPU intensive, and we can auto scale the docker containers using cloud service, and in case of peek usage, we can add queue in our system, but that will change the POST solution API  to async, and we need add another API to query the result.

## how to write test cases efficiently for all languages

We can define the test cases using test vector, define the input, and expected output using JSON format.

![image](https://github.com/user-attachments/assets/ba7f8e47-22f5-4890-b802-281cb4cbb4c1)
