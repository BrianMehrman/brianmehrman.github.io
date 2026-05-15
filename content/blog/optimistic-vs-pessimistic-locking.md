---
title: "Optimistic vs Pessimistic Locking"
date: 2018-05-29T00:00:00-05:00
tags: []
aliases:
  - /blog/2018/05/29/optimistic-vs-pessimistic-locking/
---

> Data integrity is at risk once two sessions begin to work on the same records…
- Martin Fowler

# Optimistic Locking

Is a data access strategy where it is assumed that the chance of conflict between sessions is very low. Changes from one session are validated before being committed to the database.

# Pessimistic Locking

This an opposing data access strategy assumes that conflicts between sessions is highly likely. Conflicts are prevented by forcing all transactions to obtain a lock on the data (record or table) before it can start to use it.

# Ruby on Rails

Rails has optimistic and pessimistic locking built into the `ActiveRecord` gem.

## Using Optimistic

To enable optimistic locking you only need to add a `lock_version` column to your
table schema.

```ruby
t.integer  "lock_version", default: 0
```

*

You may also specify a different column like:

```ruby
class Toy < ActiveRecord::Base
  self.locking_column = :lock_toy
end
```

Rails uses this column to keep two sessions from updating the same record at the
same time. The `lock_version` is incremented every time the record is saved. Whenever
the record is updated the correct lock version must be used when saved. If the
wrong `lock_version` is provided an error will be thrown.

When two sessions are running at the same time you can see the problem with optimistic
locking.

```irb
# Alice’s session
alice:001:0> alice = Person.find_by(name: ’Alice’)
alice:002:0> toy = Toy.find_by(name: 'racecar')
alice:003:0> toy.person_id = alice.id
alice:004:0> toy.save!
true
```

```irb
Bob’s session
bob:001:0> bob= Person.find_by(name: Bob’)
bob:002:0> toy = Toy.find_by(name: 'racecar')
bob:003:0> toy.person_id = bob
bob:004:0> toy.save!
ActiveRecord::StaleObjectError: Attempted to update a stale object: Toy.    from bob:4
```

## Using Pessimistic

With pessimistic locking there is no ‘enabling’ needed. Calling `lock` with a `find`
query is all that is needed to use pessimistic locking in Rails.

```
Toy.lock.find_by(name: 'racecar')
```

The above code will lock the `racecar` Toy until the end of the transaction. Calling lock
outside of a transaction will only lock the record for the single call.

### with_lock

There is a way to begin a transaction and lock a record in a single call. The
method `with_lock` will wrap a provided block within a `transaction` locking the
model on which it was called.

The examples below are for two pieces of code will will run at the same time in
two different sessions.

session Alice*

```ruby
alice = Person.find_by(name: 'Alice')
toy = Toy.find_by(name: 'racecar')

toy.with_lock do
  toy.person_id = alice.id
  toy.save!
end
```

*session Bob*

```ruby
bob = Person.find_by(name: 'Bob')
toy = Toy.find_by(name: 'racecar')

toy.with_lock do
  toy.person_id = bob.id
  toy.save!
end
```

One of these sessions will get the lock before the other. For this example lets
say that *Alice* got the lock first.

```bash
(Alice):004:0> toy.with_lock do
(Alice):005:1*   toy.person_id = alice.id
(Alice):006:1>   sleep(200)
(Alice):007:1>   toy.save!
(Alice):008:1> end
   (1.3ms)  BEGIN
  Toy Load (1.2ms)  SELECT  "toys".*
                    FROM "toys"
                    WHERE "toys"."id" = $1
                    LIMIT $2 FOR UPDATE  [["id", 8], ["LIMIT", 1]]
  SQL (3.3ms)  UPDATE "toys"
               SET "person_id" = 3,
                   "updated_at" = '2018-07-01 20:42:56.401262',
                   "lock_version" = 11
               WHERE "toys"."id" = $1
               AND "toys"."lock_version" = $2  [["id", 8], ["lock_version", 10]]
     (1.2ms)  COMMIT
```

*Notice the 200 second sleep. This will allow us to see what would happen with a
long running process*

```bash
(Bob):006:0> toy.with_lock do
(Bob):007:1*   toy.person_id = bob.id
(Bob):008:1>   toy.save!
(Bob):009:1> end
   (1.2ms)  BEGIN
   Toy Load (198875.2ms)  SELECT  "toys".*
                          FROM "toys"
                          WHERE "toys"."id" = $1
                          LIMIT $2 FOR UPDATE  [["id", 8], ["LIMIT", 1]]
SQL (0.9ms)  UPDATE "toys"
             SET "person_id" = 2,
                 "updated_at" = '2018-07-01 20:42:56.410077',
                 "lock_version" = 12
             WHERE "toys"."id" = $1
             AND "toys"."lock_version" = $2  [["id", 8], ["lock_version", 11]]
 (1.1ms)  COMMIT
```

We can see the affect of the lock on the *Bob* session. If you look at how long
the `Toy Load` took, `198875.2ms`. All of that time is the application waiting
on the *Alice* session.

### lock!

An alternative to `with_lock` is `lock!`. This will lock the record’s row for the
duration of the transaction.

```
ActiveRecord::Base.transaction do
  alice = Person.find_by(name: 'Alice')
  toy = Toy.find_by(name: 'racecar')
  toy.lock!
  toy.person_id = alice.id
  toy.save!
end
```

## Conclusion

Optimistic and Pessimistic locking each have their own purpose with benefits and problems.

Optimistic locking is fine to have on enabled on most user managed models. It can
be used to keep two users from updating the same record at the same time or from
outdated data. Provided that the user form has the *lock_version* passed as part
of the posted data.

Pessimistic locking provides a way to use the database layer to determine if someone
is using that data. This can be easy for updating a single table record, but becomes
most difficult the more records you try to lock. You can use pessimistic locking
to make the users wait to update their shared data or to notify the users that a
person is updating a record or set of records.

I would be careful with pessimistic locking. This can lead to database deadlocks.