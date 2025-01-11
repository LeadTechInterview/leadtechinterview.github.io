# Requirements

## Functional 

1.  View events
2.  Search events by keywords
3.  Order tickets

## Non Functional

1. Support 100m users, read heavy, read/write ratio 100:1
2. Search should be faster and return within 500 ms
3. No double booking

# Entities

- Event
- User
- Ticket
- Order

# API

- GET /events -> [event list (event name, date, available tickets)]
- GET /event/{event_id} -> [event detail (name, desc, performer, date, location, tickets]
- GET /events/search/keyword={keyword}&start={start_date}&end={end_date}&page_size={page_size}&page_num={page_num} -> [event list]
- POST /order/{event_id}
```
    {
        tickets,
        payments
    }
```

# High Level Design

## View event

![image](https://github.com/user-attachments/assets/4b55d681-b0b9-404a-959f-94a2ec64e014)

1. user make a request to view a event
2. API gateway forward request to event server
3. event server fetch event detail and return to client

## Search for event

![image](https://github.com/user-attachments/assets/47f78c5a-323a-4cd5-b45e-33d5e7eb9dda)

1. user search event by keyword
2. API gateway forward request to search server
3. search server query DB with "like" sql and return to client

## Order tickets

![image](https://github.com/user-attachments/assets/a5e12df6-5d1f-48f5-9a03-86114701a6bd)

User already have the details o the event, like tickets available, then user can order tickets.

1. user send order request (userid, tickets, event)
2. API gateway forward request to order server
3.  oder service integrates with 3rd party payment service like Strip for payment
4. order server
    -  query the availability of the tickets, and book them
    -  update the ticket status to booked once payment is done
    -  new order record is added in the same transaction.

# Deep dive

## Bad user experience when booking

User find the available ticket and then pay for them, but found that it turns out to be ordered, the problem is that we don't reserve for the tickets.  

To reserve the tickets, one way is to use DB feature like row level locking to lock the record, the problem is that it doesn't support timeout, and it's up to the application to handle the edge cases of the lock, to avoid leaving it to uncertain states, this helps on avoid double booking but it's not good to use it for reservation.

Another way is to use the status field in DB, add reserved states, and use a cron job to check the expiration on timely basis, this may have a delay in unlocking depending on the interval of the cron job.

A more preferred way is to use Redis, and it works as below:

**Reservation**
1. User select available seat and book a ticket
2. Order service add  (ticket id, user id) in Redis Distributed Lock with reservation timeout, the record will be removed automatically once timeout TTL
3. Order service creates an order record and returns the order id
4. Other users need to check the availability in Redis before booking (available in DB and not reserved in Redis)

**Payment**
1. with the order id , user can issue payment from the payment service
2. the payment service can callback order service once the payment succeeds, with the order id
3. order service write the payment detail in order record, and change the ticket status to "booked"

## How to scale up to tenth of millions of concurrent users during popular events

To cope with read heavy view events up to tenth of millions:

- we can utilize cache, since the view event is mostly unchanged
-  event service is stateless, so it can easily scale horizontally, and we can use load balance before the event services

## How to handle millions of concurrent bookings during popular events

First the user should be able to be notified that the ticket states immediately, polling won't work in this case, SSE(server send event) works for this case. But still the system may not able to handle such a kind of burst, and we need a mechanism to protect the system, we can add a toggled feature that could be enabled in this case, and park the users in a queue, and the client can use websocket to send request to the queuing service to get a token,  once order service deque a user from the queue, it can issue a token for the user, and notify the user via websocket to send the order request.

![image](https://github.com/user-attachments/assets/17098f97-99c0-4c2e-8476-8aec4e550da4)

## How to improve search to meet low latency requirements?

SQL "like" will scan the whole table, it's not efficient, we can either build full text index in DB, or use ElasticSearch with updates via CDC (Change Data Capture).

The search results can be also cached with memcached or Redis, but could be more efficient to use ElasticSearch cache b/c it's smarter with app logic (reuse cache for aggregation or filtering etc). 

![image](https://github.com/user-attachments/assets/e68e60d6-1a22-48ba-b022-ca6553d29529)
