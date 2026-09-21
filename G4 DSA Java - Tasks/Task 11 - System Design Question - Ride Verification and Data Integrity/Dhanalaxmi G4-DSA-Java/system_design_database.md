Question 1: Foreign Key Behavior

Rides.user_id references Users.user_id.

For example:

Users
user_id = 101
   ↓
Rides
ride_id = 5001
user_id = 101

If we execute:

DELETE FROM users
WHERE user_id = 101;

while Ride 5001 still references user 101, the database will normally reject the deletion if the foreign key uses the default/restrict behavior.

You would get a foreign-key constraint error.

Why?

The database prevents this situation:

Rides
ride_id | user_id
5001    | 101

Users
101 → DOES NOT EXIST

That would create an orphan record.

Therefore, the foreign key protects referential integrity.

Question 2: DELETE Strategy

I would choose:

ON DELETE RESTRICT

for historical rides.

For example:

FOREIGN KEY (user_id)
REFERENCES users(user_id)
ON DELETE RESTRICT
Why?

A ride is an important business record.

We don't want deleting a user to automatically delete:

User
 ↓
Ride
 ↓
Payment

because that could destroy important historical and financial information.

Therefore:

User deletion
      ↓
Existing rides
      ↓
Must remain

For a production system, I would usually combine this with soft deletion/anonymization, discussed below.

Question 3: Historical Data

No.

Historical rides and payments should not disappear just because the user deletes their account.

For example:

User 101
   ↓
Ride 5001
   ↓
Payment 9001

After account deletion, the system should preserve:

Ride 5001
Payment 9001

because they may be required for:

Payment reconciliation
Refunds
Disputes
Accounting
Fraud investigation
Auditing
Legal/compliance requirements

The user's personal information can instead be anonymized.

Question 4: Soft Delete vs Hard Delete

I would prefer soft delete + anonymization.

Instead of:

DELETE FROM users
WHERE user_id = 101;

we could do something like:

UPDATE users
SET
    name = NULL,
    phone = NULL,
    email = NULL,
    is_deleted = TRUE
WHERE user_id = 101;

The exact anonymization approach would depend on the application's privacy and retention requirements.

Advantages

The user record still exists:

user_id = 101
is_deleted = true

Therefore existing foreign keys remain valid.

Historical data can continue to reference:

user_id = 101

while personally identifying information is removed or protected.

Disadvantages
Database records remain.
Storage is not immediately reclaimed.
Application queries must handle is_deleted.
Anonymization must be designed carefully.
My choice
Soft delete
+
Anonymize personal information
+
Keep historical rides/payments
Question 5: Only 10,000 PINs

There are only:

0000 → 9999

which gives:

10,000 possible PINs

If there are:

10,000,000 users

we cannot give every user a unique PIN.

Instead:

User A → 4821
User B → 4821
User C → 7390
User D → 4821

This is completely valid.

The system distinguishes users using their unique identifiers:

user_id
ride_id
captain_id

The PIN is only a verification secret, not an identity.

Question 6: Should ride_pin Be UNIQUE?

No.

This would be wrong:

ride_pin VARCHAR(4) UNIQUE

because only 10,000 unique values exist.

You cannot support:

10 million users

with only:

10,000 unique PINs

The database would eventually reject duplicate PINs.

Therefore:

user_id → UNIQUE
ride_id → UNIQUE
captain_id → UNIQUE
PIN → NOT globally unique
Question 7: PIN Verification

This is unsafe:

SELECT *
FROM users
WHERE ride_pin = '4821';

Why?

Suppose:

User A → PIN 4821
User B → PIN 4821
User C → PIN 4821

The query can return multiple users.

The backend cannot know which ride the captain is trying to start.

Instead, use the active ride context.

For example:

SELECT r.*
FROM rides r
JOIN users u
    ON r.user_id = u.user_id
WHERE r.ride_id = ?
  AND r.captain_id = ?
  AND r.status = 'WAITING'
  AND u.ride_pin = ?;

Now the system verifies:

Correct ride
+
Correct captain
+
Correct status
+
Correct PIN

This is much safer.

Question 8: PIN Collision

Suppose:

User A → PIN 4821
User B → PIN 4821

Both can have the same PIN.

Suppose:

Ride 5001 → User A → Captain 200
Ride 5002 → User B → Captain 300

Captain 200 enters:

4821

The backend checks:

ride_id = 5001
captain_id = 200
status = WAITING
PIN = 4821

Therefore it matches User A's ride.

Important point

The system does not search:

"Who has PIN 4821?"

It searches:

"Does PIN 4821 match this specific active ride?"

That solves the collision problem.

Question 9: Where Should PIN Be Stored?

There are three possible designs.

Option A — PIN on User
Users
------
user_id
ride_pin

Advantages:

Simple
Easy to implement
Same PIN can be reused

Disadvantages:

Same PIN remains across rides.
If the PIN is compromised, it may affect multiple rides.
Less secure than a ride-specific PIN.
Option B — PIN on Ride
Rides
------
ride_id
user_id
captain_id
ride_pin
status

Example:

Ride 5001 → 4821
Ride 5002 → 7390
Ride 5003 → 1054
Advantages
PIN is specific to a ride.
Better security.
Easier to validate against the active ride.
PIN can expire with the ride.
Disadvantages
Requires storing a PIN for every ride.
Slightly more complex database model.
My choice

Option B is preferable for a ride-hailing system.

A ride-specific PIN limits the impact if a PIN becomes known.

Option C — Dynamically Generated PIN

Generate the PIN when the captain approaches the pickup location.

Advantages:

Stronger security
Short-lived PIN
Reduced reuse
Can expire automatically

Disadvantages:

More implementation complexity
Need expiration handling
Need reliable synchronization between rider and captain apps
My choice

For a production system:

Ride-specific temporary PIN
+
expiration
+
rate limiting

is a strong design.

Question 10: Indexing

I would create indexes such as:

PRIMARY KEY (user_id)
PRIMARY KEY (ride_id)
INDEX (user_id)
INDEX (captain_id)
INDEX (captain_id, status)
INDEX (ride_id)

for payments depending on the database's constraints/index behavior.

Why (captain_id, status)?

A common query is:

SELECT *
FROM rides
WHERE captain_id = ?
AND status = 'WAITING';

The composite index helps locate the captain's active rides quickly.

Should we index PIN?

If PIN is stored on the ride, I would not rely on PIN alone for lookup.

Instead, the primary verification lookup should be based on:

ride_id
captain_id
status

and then compare the PIN.

If the architecture frequently queries by PIN together with ride context, a composite index could be considered based on actual query patterns.

The key principle is:

Never make PIN the identity of a ride.

Question 11: Removing Primary Key

Normally, you cannot simply remove:

Users.user_id

while a foreign key depends on it.

The relationship is:

Users.user_id
      ↑
      |
Rides.user_id

The database needs the referenced column to remain a valid candidate/primary key for the foreign-key relationship.

Therefore, most relational databases will reject the operation or require the dependent foreign key/constraint to be removed first.

Question 12: Removing the Foreign Key First

Suppose we remove the foreign key.

Then remove the primary key.

Now this becomes possible:

user_id | name
--------|------
101     | Ravi
101     | Amit
101     | John

Now consider:

ride_id | user_id
--------|--------
5001    | 101

Which user does Ride 5001 belong to?

Ravi?
Amit?
John?

There is no way to know reliably.

This causes serious data integrity problems.

Possible problems include:

Duplicate user IDs
Orphaned rides
Incorrect joins
Incorrect payments
Ambiguous user identity
Broken application logic

Therefore, the primary key and foreign key constraints are essential.

Question 13: Concurrency

Suppose two requests arrive simultaneously:

Captain 1 → Start Ride 5001
Captain 2 → Start Ride 5001

Both provide the correct PIN.

We must ensure only one request succeeds.

A transaction can be used.

One good approach is an atomic conditional UPDATE.

The database itself decides which request wins.

Question 14: Atomic Ride Start

Yes, this is a very good approach:

UPDATE rides
SET status = 'STARTED'
WHERE ride_id = ?
  AND captain_id = ?
  AND status = 'WAITING';

Then check:

affected rows = 1
If:
affected rows = 1

the ride successfully changed:

WAITING → STARTED
If:
affected rows = 0

the ride was already started or the conditions were incorrect.

This is safer than:

SELECT ride
        ↓
Check status
        ↓
UPDATE ride

because two requests could both perform the SELECT before either performs the UPDATE.

The atomic UPDATE prevents that race condition.

Question 15: PIN Guessing

A 4-digit PIN has only:

10,000 combinations

Therefore, unrestricted attempts are dangerous.

The system should use several protections.

1. Rate limiting

For example:

Maximum attempts per ride
Maximum attempts per captain
Maximum attempts per device/IP
2. Maximum attempts

For example:

5 failed attempts
        ↓
Temporarily block verification

The exact threshold should be determined based on security requirements.

3. Temporary lockout

After repeated failures:

Verification temporarily disabled
4. Audit logs

Record:

ride_id
captain_id
timestamp
success/failure
device information

while respecting privacy requirements.

5. Don't expose sensitive information

The API should return a generic failure such as:

Invalid ride verification

rather than revealing whether the PIN or ride ID was the incorrect component.

Final Architecture

A good architecture would look like this:

                    RIDER
                      │
                      │ Books ride
                      ▼
                ┌─────────────┐
                │   Rides     │
                │             │
                │ ride_id     │
                │ user_id     │
                │ captain_id  │
                │ ride_pin    │
                │ status      │
                └──────┬──────┘
                       │
                       │ Captain assigned
                       ▼
              Captain reaches pickup
                       │
                       ▼
                Rider provides PIN
                       │
                       ▼
              Captain enters PIN
                       │
                       ▼
                Backend API
                       │
                       ▼
        ┌───────────────────────────┐
        │ Verify                    │
        │                           │
        │ ride_id                   │
        │ captain_id                │
        │ status = WAITING          │
        │ PIN                       │
        └─────────────┬─────────────┘
                      │
                ┌─────┴─────┐
                │           │
              VALID       INVALID
                │           │
                ▼           ▼
          Atomic UPDATE    Reject
                │
                ▼
        WAITING → STARTED

6. Usually, NO
        ride_pin varchar(4) unique
it means that no 2 rows in the table can ever have the same pin

but only from 0000 - 9999
so, oly 10000 possible pin's are available 
if we have  millions of users then, we can't assign a user permanently unique 4 digit pin.
their is a chance of getting collisions 
like:  a-3421
       b-4356
       c-9045
       .
       .
       .
       p-3421 - collision
so the ride_id should be globally unique

7. 
SELECT *
FROM users
WHERE ride_pin = '4821'; this assigns /generates same pin for every user is not authorized way.
The database may return multiple users.

The backend then has no reliable way to know which ride the captain is trying to start.

It could also expose an unnecessary user-level lookup based only on a small secret.

Instead, use the active ride context:

SELECT r.*
FROM rides r
JOIN users u
    ON r.user_id = u.user_id
WHERE r.ride_id = ?
  AND r.captain_id = ?
  AND r.status = 'WAITING'
  AND u.ride_pin = ?;

  it assigns :
  ride_id   = 5001
captain   = 200
PIN       = 4821

The database checks:

      Is ride 5001 real?
        ↓
Is captain 200 assigned to it?
        ↓
Is it currently WAITING?
        ↓
Does its rider's PIN match 4821?
        ↓
YES → allow start

8. Suppose:

User A → PIN 4821
User B → PIN 4821
User C → PIN 7390

And both A and B have active rides.

This is not a problem if the captain's request contains the ride context.

For example:

Captain A:
ride_id = 5001
captain_id = 201
PIN = 4821

The backend checks:

ride_id
+
captain_id
+
ride status
+
PIN

Only if all conditions match is the ride started.

What should identify the ride?

Primarily:

ride_id

The PIN is only one verification factor.

A good conceptual model is:

ride_id      → Which ride?
captain_id   → Who is attempting it?
status       → Is starting currently allowed?
PIN          → Does the rider's verification code match?

user_id can also be checked through the ride relationship, but the backend generally shouldn't trust a client-supplied user_id as the authority when it can derive it from ride_id.

9. 


10. First, primary keys normally already have indexes.

So:

Users(user_id)
Rides(ride_id)

would normally already be indexed because they are primary keys.

Useful indexes include:

Rides(user_id)
Rides(captain_id)
Rides(captain_id, status)
Payments(ride_id)
Why?

Suppose you frequently ask:

SELECT *
FROM rides
WHERE captain_id = ?
AND status = 'WAITING';

Then:

(captain_id, status)

is a useful composite index.

For:

SELECT *
FROM rides
WHERE user_id = ?;

you want:

Rides(user_id)

For:

SELECT *
FROM payments
WHERE ride_id = ?;

you want:

Payments(ride_id)
What about ride_pin?

Don't automatically create:

INDEX(ride_pin)

just because it's a PIN.

If your actual verification query searches by:

ride_id
+
captain_id
+
status
+
PIN

the highly selective ride identifier is already doing the important lookup.

You can decide whether a PIN index is useful based on the actual query plan and workload.

More importantly:

Never design the system around searching the entire database for a PIN.

Avoid:

SELECT *
FROM rides
WHERE ride_pin = '4821';

Use:

WHERE ride_id = ?
AND captain_id = ?
AND status = 'WAITING'
AND ride_pin = ?


11. Normally, no.

Suppose:

Users
  user_id ← PRIMARY KEY

       ↑
       |
       |
Rides
  user_id ← FOREIGN KEY

The foreign key depends on the referenced key.

The database will normally reject an attempt to remove the referenced primary key while that foreign-key relationship exists.

The exact error/behavior depends on the database system and its constraints, but the general principle is:

A referenced key cannot simply be removed while another table depends on it.

This is an important database-integrity protection.

12. Suppose someone does:

ALTER TABLE rides
DROP FOREIGN KEY fk_rides_user;

and then:

ALTER TABLE users
DROP PRIMARY KEY;

Now the database has lost an important integrity guarantee.

For example:

Users

user_id | name
--------|------
101     | Ravi
101     | Amit
101     | John

Depending on the database schema and other constraints, duplicate user_id values may now be possible because user_id is no longer enforcing uniqueness as a primary key.

Then:

Rides

ride_id | user_id
--------|--------
5001    | 101

What does 101 mean?

Ravi?
Amit?
John?

There is no longer a guaranteed single parent.

That's exactly what relational constraints are designed to prevent.

The chain is:
PRIMARY KEY
     ↓
uniquely identifies user
     ↓
FOREIGN KEY references that identity
     ↓
rides reliably point to one user

Removing that structure can produce:

orphaned records
ambiguous relationships
duplicate identities
invalid references
inconsistent application behavior


13. Consider:

Device 1 → Start Ride 5001 with 4821
Device 2 → Start Ride 5001 with 4821

Both requests arrive almost simultaneously.

A bad implementation is:

Request 1:
SELECT → WAITING

Request 2:
SELECT → WAITING

Request 1:
UPDATE → STARTED

Request 2:
UPDATE → STARTED

Both requests saw WAITING.

You need the database to make the state transition atomic.

Possible techniques include:

1. Transaction

Perform verification and state change inside an appropriate transaction.

2. Row-level locking

Lock the ride row while processing the transition.

Conceptually:

Request 1
   ↓
lock ride 5001
   ↓
verify
   ↓
STARTED
   ↓
commit
   ↓
unlock

Request 2 then sees the updated state.

3. Atomic conditional update

Often the simplest approach is:

UPDATE rides
SET status = 'STARTED'
WHERE ride_id = ?
  AND captain_id = ?
  AND status = 'WAITING';


14. This is safer:

UPDATE rides
SET status = 'STARTED'
WHERE ride_id = ?
  AND captain_id = ?
  AND status = 'WAITING';

Then:

affected rows = 1
    ↓
success

If:

affected rows = 0

then the ride wasn't in the expected state or the captain wasn't authorized.

Why?

The database checks the condition and performs the state change as one operation.

Imagine two requests:

Initial:
WAITING

Request A:

UPDATE ... WHERE status='WAITING'

→ 1 row updated.

Now:

STARTED

Request B runs the same statement:

UPDATE ... WHERE status='WAITING'

→ 0 rows updated.

Therefore:

Request A → starts ride
Request B → rejected

This avoids the classic:

SELECT
   ↓
gap
   ↓
UPDATE

race condition.


15. A 4-digit PIN has only:

10,000 combinations

So the system must not allow unlimited attempts.

For example, use several layers:

Rate limiting

Limit attempts per:

captain_id
+
ride_id
+
device/session
+
IP where appropriate
Maximum attempts

For example:

5 failed attempts
       ↓
temporarily block verification

The exact number is a product/security decision.

Temporary lockout

After repeated failures:

WAITING
   ↓
too many failures
   ↓
verification temporarily locked
Audit logs

Record events such as:

ride_id
captain_id
timestamp
success/failure
attempt metadata

Avoid logging the actual PIN in ordinary application logs.

Why captain identity matters

Don't merely say:

5 attempts per IP

because multiple users can share an IP.

Instead, combine controls around the authenticated captain and ride.

Final Architecture

A clean architecture would look like this:

                 RIDER
                   │
                   │ Books ride
                   ↓
             ┌─────────────┐
             │    RIDES    │
             │-------------│
             │ ride_id     │
             │ user_id     │
             │ captain_id  │
             │ ride_pin    │
             │ status      │
             └──────┬──────┘
                    │
             Captain assigned
                    │
                    ↓
           Captain reaches pickup
                    │
                    ↓
             Rider provides PIN
                    │
                    ↓
           Captain enters PIN
                    │
                    ↓
              Backend API
                    │
                    ↓
        ┌──────────────────────┐
        │ Verify               │
        │                      │
        │ ride_id              │
        │ captain_id           │
        │ status = WAITING     │
        │ PIN                  │
        └──────────┬───────────┘
                   │
             ┌─────┴─────┐
             │           │
           VALID       INVALID
             │           │
             ↓           ↓
        Atomic UPDATE   Reject
             │
             ↓
      status = STARTED
             │
             ↓
        Ride begins
How this handles millions of users

The important thing is that the system doesn't search millions of users by PIN.

Bad:

WHERE ride_pin = '4821'

Good:

WHERE ride_id = ?
AND captain_id = ?
AND status = 'WAITING'
AND ride_pin = ?

ride_id is the main identity and can be indexed efficiently.

How it handles only 10,000 PINs

Duplicate PINs are allowed:

Ride 5001 → 4821
Ride 8127 → 4821
Ride 9344 → 4821

No problem.

The combination is effectively:

ride_id + PIN

and authorization also checks the captain.

How foreign keys help

Relationship:

Users
  user_id
     ↑
     │ FK
     │
Rides
  user_id

This prevents a ride from referring to a nonexistent user, assuming normal FK enforcement.

Similarly:

Captains
   ↑
   │
Rides.captain_id

can maintain the captain relationship.

User deletion

This requires a deliberate policy.

You generally don't want:

User deleted
    ↓
All historical rides disappear
    ↓
Payment history disappears

because rides and payments may need to be retained for business, accounting, dispute, or regulatory reasons.

A common approach is soft deletion:

Users
----------------
user_id
name
deleted_at

Instead of physically deleting the user:

deleted_at = current timestamp

Historical rides can continue referencing that user.

Another approach is to retain an appropriate historical/customer record while restricting access to deleted users.

The exact retention requirements depend on the application's legal and business requirements.

Payment Data

Payments should generally have their own table:

Payments
----------------
payment_id
ride_id
amount
status
transaction_reference
created_at

with:

Payments.ride_id
        ↓
Rides.ride_id

This creates:

User
 ↓
Ride
 ↓
Payment

Don't make the PIN responsible for identifying payment records.

The stable identifier is:

ride_id
Index Strategy

A reasonable starting point:

Users
 └── PRIMARY KEY(user_id)

Rides
 ├── PRIMARY KEY(ride_id)
 ├── INDEX(user_id)
 ├── INDEX(captain_id)
 └── INDEX(captain_id, status)

Payments
 ├── PRIMARY KEY(payment_id)
 └── INDEX(ride_id)

Additional indexes should be based on actual query patterns and query plans rather than indexing every column.

Primary-Key Removal

The database should normally prevent:

Users.user_id PRIMARY KEY
          ↓
Rides.user_id FOREIGN KEY

from being broken accidentally.

If someone deliberately removes the FK first and then removes the PK, they have weakened the database's integrity guarantees.

So this isn't merely a syntax issue.

It changes the fundamental reliability of the data model.

Complete Interview Answer

If the interviewer asks:

"Why don't you make a 4-digit ride PIN globally unique?"

A strong answer is:

A 4-digit PIN has only 10,000 possible values, so it cannot scale as a globally unique identifier for millions of users or rides. The PIN should be treated as a verification secret rather than the identity of a ride. The ride is uniquely identified by ride_id, and PIN verification should happen within the context of that ride and authenticated captain. The backend should verify the ride_id, captain_id, expected ride status, and PIN, then perform an atomic state transition from WAITING to STARTED. Duplicate PINs are therefore safe because two rides can have the same PIN while still having different ride_id values. Rate limiting and attempt limits are also required because the PIN space contains only 10,000 possibilities.

The core principle to remember is:

              IDENTITY              VERIFICATION
                 │                       │
                 ↓                       ↓
              ride_id                  PIN
                 │                       │
                 └──────────┬────────────┘
                            ↓
                     AUTHORIZED?
                            ↓
                     START THE RIDE

r