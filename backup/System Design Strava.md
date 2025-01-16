# Requirements (5 mins):

## Functional Requirements

> Identify core features (e.g., "Users should be able to post tweets"). Prioritize 2-3 key features.

1. users can start/stop/pause their activity (runs/rides)
2. users should be able to check the activity data (route, distance, time etc) when running /riding
3. users should be able to check history records, including friends

## Non-Functional Requirements

> Focus on system qualities like scalability, latency, and availability, consistency, security, durability, fault tolerance. Quantify where possible (e.g., "render feeds in under 200ms").

1. the system should be highly available
2. the app should work offline when no Internet
3. the stats should be accurate
4. should support 10m concurrent users

## Capacity Estimation

> Skip unnecessary calculations unless they directly impact the design (e.g., sharding in a TopK system).

route GPS data estimation: 100m DAU, 10m concurrent users, collect interval 5 secs, 30 min activity per day will generate 30 x 60 / 5 = 360 records,  so around 36,000m records per day, each record we have around 40 Byte.

Storage: 36,000m x 40 = 1440GB per day.  1500G x 360 = 180000x3G = 540000 GB = 540 TB

QPS 3600m / 100k = 36k

## Core Entities (2 mins)

>  Identify key entities (e.g., User, Tweet, Follow) to define the system's foundation.

- user
- activity
- route
- friend

# API/System Interface (5 mins)

> Define the contract between the system and users. Prefer RESTful APIs unless GraphQL is necessary.

- POST /activity -> Activity, create an activity, body {type}
- PATCH /activity/id, change status, body { status}, start, stop, pause, complete
- POST /activity/id/route, update route geo tracking { geolocation}
- GET /activities/page?mode=user|friend&page=

# [Optional] Data Flow (5 mins)

> Describe high-level processes for data-heavy systems (e.g., web crawlers).

# High-Level Design (10-15 mins)

> Draw the system architecture, focusing on core components (e.g., servers, databases). Keep it simple and iterate based on API endpoints.

![image](https://github.com/user-attachments/assets/68ced8bb-4916-49f8-a3cd-8d56576a1688)


# Deep Dives (10 mins)

> Address non-functional requirements, edge cases, and bottlenecks. Proactively improve the design (e.g., scaling, caching, database sharding).

## No internet connection

We can save data in local db and sync to server later

## Support 100 DAU, 10m concurrent users

storage: 540TB/year

- purge old storage of route date
- shard data by time
-  use cold storage to save cost
-  compress the date

QPS: 40k

- use nosql database, time series database more specific 
- clients aggregate the data and use longer intervals if not in realtime view
- shard the data by user id, activity id to distribute the load across servers

