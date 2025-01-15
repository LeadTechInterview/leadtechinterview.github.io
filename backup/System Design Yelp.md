# Requirements (5 mins):

## Functional Requirements

> Identify core features (e.g., "Users should be able to post tweets"). Prioritize 2-3 key features.

- *Users can add/del/update a business (not in scope)*
- Users can find nearby places by name, location, category
- Users can view the business
- Users can add reviews and ratings

## Non-Functional Requirements

> Focus on system qualities like scalability, latency, and availability. Quantify where possible (e.g., "render feeds in under 200ms").

- The search should be fast and return in 500 ms
- The system should be highly available, eventual consistency is fine
- The system should be scalable to support 100m DAU, 10m business
- One user could only leave one review for one business

## Capacity Estimation

> Skip unnecessary calculations unless they directly impact the design (e.g., sharding in a TopK system).

- read heavy: 100m x 10 reads/day / 100,000 = 10 k QPS
- review storage: r10m x 100 reviews x 1000 Byte = 1 TB

## Core Entities (2 mins)

>  Identify key entities (e.g., User, Tweet, Follow) to define the system's foundation.

- Business
- Users
- Reviews

# API/System Interface (5 mins)

> Define the contract between the system and users. Prefer RESTful APIs unless GraphQL is necessary.

- search, GET /businesses?keyword&location&category&page -> [business]
- view biz detail, GET /bussiness/id -> bussiness detail
- view reviews, GET /bussiness/reviews?page -> [reviews]
- review, POST /bussiness/review -> 200 OK, body { comments, rating }

# [Optional] Data Flow (5 mins)

> Describe high-level processes for data-heavy systems (e.g., web crawlers).

# High-Level Design (10-15 mins)

> Draw the system architecture, focusing on core components (e.g., servers, databases). Keep it simple and iterate based on API endpoints.

![image](https://github.com/user-attachments/assets/ab66c559-967c-4e2b-98e5-2a222740b8d4)

# Deep Dives (10 mins)

> Address non-functional requirements, edge cases, and bottlenecks. Proactively improve the design (e.g., scaling, caching, database sharding).

![image](https://github.com/user-attachments/assets/6ac15476-9a6d-43c5-a6c7-68eb56f13979)

## search within 500 ms

sql range query for lat/long is not efficient, we can either use geo index or quad tree. For keyword search, we could use the reverse index. ElasticSearch support those features, and we can use CDC (Change Data Capture) feature in DB to sync the data using queue/stream. However this adds extra service and introduces some complexity in the system.  Based on our estimation above, the database size is around 1TB, and we can actually use Postgres with PostGIS plugin in this case, it support full text search as well as geo indexing.

## search by predefined names like city etc

Search within radius won't work in this case b/c the shape is polygons. We can download polygons data from [geoapify](https://www.geoapify.com/download-all-the-cities-towns-villages) and add location table:

- name
- type (city/neighborhood etc)
- polygons

Postgres with PostGIS plugin and Elasticsearch both support polygons query. However it's not efficient, and we can precompute the location names and stored in DB to avoid compute on each query, and compute once when the record is created.

## how to update avg rating

Query all review records for one business and calculating the score is not optimal, we can actually update it once new review record added, using a iterative formulae to calculate the avg value, so basically $avg(n) = [r(n) + r(n-1) + ... + 1] / n = r(n) / n + avg(n-1) \times (n-1) / n$, however there could be concurrency update issues, which could be either via row level lock or optimistic concurrency control introduced in [Avoid double booking](https://leadtechinterview.github.io/post/Avoid%20double%20booking.html)

## one review per user per biz

application level checking works, but would be better to have the constraints at DB layer

```
ALTER TABLE reviews
ADD CONSTRAINT unique_user_business UNIQUE (user_id, business_id);
```
