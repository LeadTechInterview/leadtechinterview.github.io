# Requirements

## Functional

- give a long url, return a short url
- short url redirect to long url
- customize short url
- set expiration date

## NonFunctional

- scalability 100M users, read/write ratio 10:1
- redirect should be fast, less than 100 ms
- uniqueness of short url for the same long url

# Entities

- original url
- short url
- user

# API

1. POST /url
input:
```
{
   original url,
   customized code (optional),
   expiration date (optional)
}
```

output: 
```
{
   short url
}
```

2. GET /{shortcode}

302 redirect to original url

# High level design

![image](https://github.com/user-attachments/assets/5b22ba65-0f1b-4a7d-8d28-5a6b0d979fab)

# Deep dive

## Uniqueness of url

Use hash function or random string, if worry about conflict, we can either regenerating random number  or adding salt when generating hash, or we can add username in the url (like namespace). 

For short url, we should use B62 encoding, and truncate the string generated to a small number of characters.

The global counter may work but adds extra service in the system, and it may fail and add more complexity there.

## Fast redirect

First the DB query should be fast, we can add index for short code, besides we can cache the short code in memory, and use LRU  cache policy. We can also utilize CDN, some CDN providers provide some basic computing at the Edge so we can just redirect there.

## scale 100M users

Assume each user uses the redirect service 10 times per day, and create 1 short url per day:

QPS:
- 100M writes per day, 100M / 100k = 1k QPS 
- 100M x 10 reads per day, 10k QPS

Split into read/write services

record size:
- original url (100 bytes)
- short url (50 bytes)
- expire date ( 8 bytes)
- create date (8 bytes)
- user id (8 bytes)
total ~ 200 bytes

storage (1 year)
- url table 200 bytes x 100m x 1 x 365 = 20G x 360 = 7200 GB = 7.2 TB

we can use nosql db  like DynamoDB which can:
- easily handle ~1k writes/sec and ~10k reads/sec.
- scales horizontally with sharding and replication.

![image](https://github.com/user-attachments/assets/2b479030-1436-4fd8-b625-0fae2cfc446e)


