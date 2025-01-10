# Problem

Imagine two users try to buy the last ticket to a show at almost the same instant. Without a proper system, it's possible both users could be told they successfully bought the ticket, leading to overselling.

# Solutions

To ensure that no two users book the same ticket simultaneously, the Booking Service uses database transactions with ACID properties, employing techniques like row-level locking or optimistic concurrency control (OCC).

- **Row-Level Locking**: This is one technique to achieve isolation. When a user starts booking a ticket, the database places a "lock" on the specific row in the database table that represents that ticket. This lock prevents any other transaction from modifying that row until the first transaction is finished (either committed or rolled back). Think of it like putting a "reserved" sign on the ticket.

- **Optimistic Concurrency Control (OCC)**: This is an alternative to locking. Instead of locking the row, the system checks if the data has been modified by another transaction before committing the current transaction. It typically does this by comparing a version number or timestamp. If the data has changed in the meantime, the transaction is aborted, and the user might be informed that the ticket is no longer available. This is "optimistic" because it assumes conflicts are rare.

## Row-Level Locking

Scenario: Imagine a database table storing airline seat reservations. Each row represents a seat on a specific flight.

| Flight | Seat | Customer |
|---|---|---|
| AA123 | 1A | John Doe |
| AA123 | 1B | Jane Smith |
| AA123 | 2A |  |
| AA123 | 2B |  |

Two users try to book seat 2A simultaneously:

1. User 1 clicks to book seat 2A. The database transaction begins.
2. The database places a lock on the row representing seat 2A.
3. User 2, at the exact same moment, also clicks to book seat 2A. Their transaction also begins.

However, because User 1's transaction has already locked the row, User 2's transaction is blocked. It has to wait.
User 1 completes their booking (payment goes through, etc.). The transaction commits, and the lock on seat 2A is released.

Now, User 2's transaction can proceed. But when it tries to access the row for seat 2A, it sees that it's no longer available (John Doe has it). The system informs User 2 that the seat is taken.

## Optimistic Concurrency Control (OCC)

Scenario: A similar airline seat reservation system. This time, instead of locks, each row has a version number.

| Flight | Seat | Customer | Version |
|---|---|---|---|
| AA123 | 1A | John Doe | 1 |
| AA123 | 1B | Jane Smith | 1 |
| AA123 | 2A |  | 1 |
| AA123 | 2B |  | 1 |

Two users try to book seat 2A simultaneously:

1. User 1 starts the booking process for seat 2A. The system reads the row and notes the version number (1).
2. User 2, at almost the same time, also starts booking seat 2A. Their system also reads the row and notes the version number (1).
3. User 1 completes their booking. The system checks if the version number in the database is still 1. It is, so the system updates the row with User 1's information and increments the version number to 2.
4. User 2 completes their booking a fraction of a second later. Their system also checks if the version number is still 1. But now, it's 2! This means the row has been modified by another transaction (User 1's).
5. User 2's transaction is aborted. The system informs them that the seat is no longer available.

## Key Differences

### Locking (Pessimistic):

- Locks are acquired immediately, preventing conflicts upfront.
- Can lead to performance issues if there are many concurrent users trying to access the same data (because of waiting).
- Better for situations where conflicts are likely (e.g., very popular events).

### OCC (Optimistic):

- Assumes conflicts are rare and only checks for them at the end.
- Generally better performance for most applications because there's no waiting.
- More complex to implement because you need to handle the cases where transactions are aborted.
- Both methods are used to ensure data integrity in concurrent environments, but they have different trade-offs in terms of performance and complexity.

# MySQL examples

Here are some MySQL examples demonstrating row-level locking and how it would conceptually work (MySQL doesn't directly expose OCC in the same way). I'll also explain how OCC would be implemented in SQL conceptually.

## Row-Level Locking (using FOR UPDATE)

MySQL uses FOR UPDATE to acquire exclusive row-level locks.

1. Table Setup:

```SQL
CREATE TABLE tickets (
    id INT PRIMARY KEY AUTO_INCREMENT,
    event_name VARCHAR(255),
    available_seats INT
);

INSERT INTO tickets (event_name, available_seats) VALUES ('Concert X', 10);
```

2. Booking Process (simulating two concurrent users):

- User 1 (in one MySQL session):

```SQL
START TRANSACTION; -- Start a transaction

SELECT available_seats FROM tickets WHERE id = 1 FOR UPDATE; -- Lock the row

-- Check if seats are available
SET @seats := (SELECT available_seats FROM tickets WHERE id = 1);
IF @seats > 0 THEN
    UPDATE tickets SET available_seats = available_seats - 1 WHERE id = 1;
    SELECT 'Booking successful' AS message;
    COMMIT; -- Commit the transaction, releasing the lock
ELSE
    SELECT 'No seats available' AS message;
    ROLLBACK; -- Rollback the transaction
END IF;
```

- User 2 (in a separate MySQL session, running at almost the same time):

```SQL
START TRANSACTION;

SELECT available_seats FROM tickets WHERE id = 1 FOR UPDATE; -- This will block until User 1's transaction commits

-- (Once User 1 commits, this continues)
SET @seats := (SELECT available_seats FROM tickets WHERE id = 1);
IF @seats > 0 THEN
    UPDATE tickets SET available_seats = available_seats - 1 WHERE id = 1;
    SELECT 'Booking successful' AS message;
    COMMIT;
ELSE
    SELECT 'No seats available' AS message;
    ROLLBACK;
END IF;
```

3. Explanation:

>START TRANSACTION begins a transaction.
SELECT ... FOR UPDATE acquires an exclusive lock on the selected row. This prevents other transactions from modifying the row until the current transaction is committed or rolled back.
If User 2 tries to execute the SELECT ... FOR UPDATE while User 1's transaction holds the lock, User 2's query will wait.
COMMIT makes the changes permanent and releases the lock.
ROLLBACK undoes any changes and releases the lock.

## Conceptual OCC in SQL (using a version column)

MySQL doesn't have built-in OCC like some other databases, but you can implement it yourself using a version column:

1. Table Setup (with a version column):

```SQL
ALTER TABLE tickets ADD COLUMN version INT UNSIGNED NOT NULL DEFAULT 0;
```

2. Booking Process:

```SQL
START TRANSACTION;

SELECT available_seats, version FROM tickets WHERE id = 1 INTO @seats, @version;

IF @seats > 0 THEN
    UPDATE tickets 
    SET available_seats = available_seats - 1, version = version + 1
    WHERE id = 1 AND version = @version; -- Check the version here!

    IF ROW_COUNT() > 0 THEN -- Check if the update was successful
        SELECT 'Booking successful' AS message;
        COMMIT;
    ELSE
        SELECT 'Booking failed (concurrent update)' AS message;
        ROLLBACK;
    END IF;
ELSE
    SELECT 'No seats available' AS message;
    ROLLBACK;
END IF;
```

3. Explanation of OCC Implementation:

We retrieve the available_seats and the version number in one SELECT.
The crucial part is the `WHERE id = 1 AND version = @version` clause in the UPDATE statement. This condition ensures that the update only happens if the version number in the database still matches the version number we read earlier.
ROW_COUNT() checks how many rows were affected by the UPDATE. If it's 0, it means the version has changed (another transaction modified the row), and the booking fails.

This is a simplified example. In a real application, you would need to handle the "booking failed" case gracefully, perhaps by retrying the booking or informing the user.

These examples illustrate the core concepts of row-level locking and OCC in MySQL. They are simplified for clarity but demonstrate the key mechanisms involved. Remember that actual performance and behavior can be affected by factors like database configuration, indexes, and transaction isolation levels.

> [!NOTE]
>  The behavior of row-level locking without FOR UPDATE depends heavily on the transaction isolation level set for your MySQL session or globally for the server.
>
> - **Default Isolation Level (REPEATABLE READ)**: In MySQL's default REPEATABLE READ isolation level, a simple SELECT statement within a transaction does not acquire any locks that prevent other transactions from modifying the selected rows. This means that if you perform a SELECT and then later try to UPDATE based on the data you read, you could encounter a "lost update" problem.
> - **READ COMMITTED Isolation Level**: In READ COMMITTED, a SELECT statement reads only committed data. However, it still doesn't acquire locks that prevent other transactions from modifying the data after the SELECT has finished. So, the lost update problem can still occur.
> - **SERIALIZABLE Isolation Level**: This is the highest isolation level. In SERIALIZABLE, even a simple SELECT statement acquires shared locks that prevent other transactions from modifying the selected rows. This prevents lost updates and other concurrency problems, but it can also significantly reduce concurrency and performance.
> - **Using FOR UPDATE (Pessimistic Locking)**: As discussed before, FOR UPDATE explicitly acquires an exclusive lock on the selected rows, regardless of the transaction isolation level (except in some very specific edge cases related to storage engines). This is the most reliable way to prevent concurrency issues like lost updates when you need to update data based on a previous read.