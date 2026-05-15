---
title: "Postgresql Record Locking"
date: 2018-06-23T00:00:00-05:00
tags: []
aliases:
  - /blog/2018/06/23/database-record-locking/
---

# Database Lock Modes

Postgresql provides different locking modes to control concurrency within the database.
Locks are automatically acquired by most Postgres commands to make sure tables are not
dropped or modified while a command is executed. Locks can also be acquired manually
for application level control.

ActiveRecord provides the interface between the Rails application and the database.
Using ActiveRecord we can create these database level locks.

## Setup

For the examples in this post we will be using the following data setup with a new Rails application.

```ruby
# Create the tables
class CreatePeople < ActiveRecord::Migration
  def change
    create_table :people do |t|
      t.string :name
      t.timestamps
    end
  end
end

class CreateToys < ActiveRecord::Migration
  def change
    create_table :toys do |t|
      t.string :name
      t.references :person, foreign_key: true
      t.timestamps
    end
  end
end

# Add Models
class Toy < ApplicationRecord
  belongs_to :person, required: false
end

class Toy < ApplicationRecord
  belongs_to :person, required: false
end

# Generate some data
people = %w(Dave Bob Alice Ace Bee Chip)

people.each { |name| Person.find_or_create_by({ name: name }) }

toys = %w(rocket plane ship yo-yo bike boat truck blocks house doll racecar thermonuclearbomb)

toys.each { |toy_name| Toy.find_or_create_by({ name: toy_name }) }
```

## Table Level Locks

This table shows the which locks on a table (columns) will block a requested lock (rows).

| Requested Lock Mode | ACCESS SHARE | ROW SHARE | ROW EXCLUSIVE | SHARE UPDATE EXCLUSIVE | SHARE | SHARE ROW EXCLUSIVE | EXCLUSIVE | ACCESS EXCLUSIVE |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| ACCESS SHARE |  |  |  |  |  |  |  | X |
| ROW SHARE |  |  |  |  |  |  | X | X |
| ROW EXCLUSIVE |  |  |  |  | X | X | X | X |
| SHARE UPDATE EXCLUSIVE |  |  |  | X | X | X | X | X |
| SHARE |  |  | X | X |  | X | X | X |
| SHARE ROW EXCLUSIVE |  |  | X | X | X | X | X | X |
| EXCLUSIVE |  | X | X | X | X | X | X | X |
| ACCESS EXCLUSIVE | X | X | X | X | X | X | X | X |

### Access Share

```bash
AccessShareLock
```

Access share is the most common lock and is only blocked by an `ACCESS EXCLUSIVE`.

A simple select on any table will acquire the `ACCESS SHARE` lock. Any table references
for a query can create a lock on that referenced table.

```ruby
Toy.find_by(name: 'racecar')
```

The `find_by` method on an ActiveRecord object is an easy way to acquire the
`ACCESS SHARE` lock. These locks only there while the query is running. A normal
selection of a single row will not block the selection of the same row by another
connection.

### Share Row Exclusive

```bash
ShareRowExclusiveLock
```

The `SHARE ROW EXCLUSIVE` is a lock that is not automatically acquired by any of the
PostgreSQL commands. This lock is created by the user or the application that is
connected to PostgreSQL.

```ruby
toy = Toy.find_by(name: 'racecar')
toy.lock!
toy.save!
```

In Rails you can acquire this lock by using the `lock!` method. This method is used
by developers who want to use a *Pessimistic* locking strategy.

### Access Exclusive

```bash
AccessExclusiveLock
```

The `ACCESS EXCLUSIVE` lock will block all other locks from being acquired.

This lock is acquired by the DROP TABLE, TRUNCATE, REINDEX, CLUSTER, VACUUM FULL,
and REFRESH MATERIALIZED VIEW. The command ALTER TABLE can also acquire an
`ACCESS EXCLUSIVE`.

```ruby
ActiveRecord::Migration.add_column :toys, :counter, :integer
```

Adding a column to a table will acquire an `ACCESS EXCLUSIVE` lock.

For more information on the other database level locks and what commands create
these locks please check out the PostgreSQL documentation [here](https://www.postgresql.org/docs/9.4/static/explicit-locking.html).

## Deadlock

A deadlock is where 2 or more transactions attempt to acquire a lock the other holds.
When this occurs neither process can proceed. When this occurs PostgreSQL will
resolve the dead lock by aborting one of the transactions involved.

### Example

Transaction A sends a request  to update the user on several toys. This requires
locks to be acquired on the `Toys` table.

```ruby
# Transaction A: Bob picks up 5 toys
ActiveRecord::Base.transaction do
  bob = Person.find_by(name: 'Bob')
  names = %w(bike yo-yo racecar truck rocket)

  names.each do |toy_name|
    toy = Toy.find_by(name: toy_name)
    toy.lock!
    toy.person = bob
    sleep(5)
    toy.save
  end
end
```

Transaction B sends a request to update the user on several toys as well. This
list includes a couple of the same toys the first transaction is going to update.

```ruby
# Transaction B: Alice picks up 5 toys.
ActiveRecord::Base.transaction do
  alice = Person.find_by(name: 'Alice')
  names = %w(rocket plane ship doll bike)

  names.each do |toy_name|
    toy = Toy.find_by(name: toy_name)
    toy.lock!
    toy.person = alice
    sleep(5)
    toy.save
  end
end
```

If both of these transactions are run at the same time you will see a dead lock
occur when transaction A tries to acquire a lock on the same record transaction
B has already acquired a lock for.

```bash
  (1.1ms)  BEGIN
  Person Load (0.8ms)  SELECT  "people".* FROM "people" WHERE "people"."name" = $1 LIMIT $2  [["name", "Alice"], ["LIMIT", 1]]
  Toy Load (0.8ms)  SELECT  "toys".* FROM "toys" WHERE "toys"."name" = $1 LIMIT $2  [["name", "rocket"], ["LIMIT", 1]]
  Toy Load (0.7ms)  SELECT  "toys".* FROM "toys" WHERE "toys"."id" = $1 LIMIT $2 FOR UPDATE  [["id", 1], ["LIMIT", 1]]
  Toy Load (1.4ms)  SELECT  "toys".* FROM "toys" WHERE "toys"."name" = $1 LIMIT $2  [["name", "bike"], ["LIMIT", 1]]
  Toy Load (1002.7ms)  SELECT  "toys".* FROM "toys" WHERE "toys"."id" = $1 LIMIT $2 FOR UPDATE  [["id", 5], ["LIMIT", 1]]
   (1.1ms)  ROLLBACK
ActiveRecord::Deadlocked: PG::TRDeadlockDetected: ERROR:  deadlock detected
DETAIL:  Process 1976 waits for ShareLock on transaction 82071; blocked by process 1978.
Process 1978 waits for ShareLock on transaction 82070; blocked by process 1976.
HINT:  See server log for query details.
CONTEXT:  while locking tuple (0,29) in relation "toys"
: SELECT  "toys".* FROM "toys" WHERE "toys"."id" = $1 LIMIT $2 FOR UPDATE
    from (irb):75:in `block (2 levels) in irb_binding'
    from (irb):73:in `each'
    from (irb):73:in `block in irb_binding'
    from (irb):69
```

## Displaying Locks

The locks acquired in PostgreSQL can be viewed using the `psql` client to query
the PostgreSQL server.

### Example

For this example we will need to open three connections to our rails console. To
see these locks in action we will need to first add a lock to the database that
will block all subsequent queries.

In one of the rails consoles we will be creating a lock on the `Toys` table
using the following command

```ruby
ActiveRecord::Base.transaction do
  toy = Toy.find_by(name: 'racecar')
  toy.lock!
  sleep(3600) # wait for an hour
  toy.save!
end
```

This will create an `ExclusiveLock`. This will not block an `AccessShareLock`,
but it will block an update. We will next try to update the same record from our
second rails console.

```ruby
toy = Toy.find_by(name: 'racecar')
toy.update(person_id: 1)
```

This update will attempt to acquire a `ShareLock` on the `toys` table. The
`ExclusiveLock` will block the update. You can view this lock by quering the
`pg_catalog.pg_locks` and the `pg_catalog.pg_stat_activity`.

Using this SQL query below we can see which statement is blocking another.

```SQL
SELECT blocked_locks.pid             AS blocked_pid,
  blocked_activity.usename           AS blocked_user,
  blocking_locks.pid                 AS blocking_pid,
  blocking_activity.usename          AS blocking_user,
  blocked_activity.application_name  AS blocked_application,
  blocking_activity.application_name AS blocking_application,
  blocked_activity.query             AS blocked_statement,
  blocking_activity.query            AS current_statement_in_blocking_process,
  blocked_locks.mode                 AS blocked_mode,
  blocking_locks.mode                AS blocking_mode
FROM pg_catalog.pg_locks         blocked_locks
JOIN pg_catalog.pg_stat_activity blocked_activity
  ON blocked_activity.pid      = blocked_locks.pid
JOIN pg_catalog.pg_locks         blocking_locks
  ON blocking_locks.locktype   = blocked_locks.locktype
  AND blocking_locks.DATABASE      IS NOT DISTINCT FROM blocked_locks.DATABASE
  AND blocking_locks.relation      IS NOT DISTINCT FROM blocked_locks.relation
  AND blocking_locks.page          IS NOT DISTINCT FROM blocked_locks.page
  AND blocking_locks.tuple         IS NOT DISTINCT FROM blocked_locks.tuple
  AND blocking_locks.virtualxid    IS NOT DISTINCT FROM blocked_locks.virtualxid
  AND blocking_locks.transactionid IS NOT DISTINCT FROM blocked_locks.transactionid
  AND blocking_locks.classid       IS NOT DISTINCT FROM blocked_locks.classid
  AND blocking_locks.objid         IS NOT DISTINCT FROM blocked_locks.objid
  AND blocking_locks.objsubid      IS NOT DISTINCT FROM blocked_locks.objsubid
  AND blocking_locks.pid != blocked_locks.pid

JOIN pg_catalog.pg_stat_activity blocking_activity ON blocking_activity.pid = blocking_locks.pid
WHERE NOT blocked_locks.GRANTED;
```

This query should show you something that looks like this.

```bash
blocked_pid | blocked_user | blocking_pid | blocking_user |                                            blocked_statement                                            |                                  current_statement_in_blocking_process                                  | blocked_application | blocking_application |    blocked_mode     |    blocking_mode
-------------+--------------+--------------+---------------+---------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------+---------------------+----------------------+---------------------+---------------------
        2210 | postgres     |         2436 | postgres      | UPDATE "toys" SET "person_id" = $1, "updated_at" = $2 WHERE "toys"."id" = $3                            | SELECT  "toys".* FROM "toys" WHERE "toys"."id" = $1 LIMIT $2 FOR UPDATE                                 | rails_console       | rails_console        | ShareLock           | ExclusiveLock
```

Next we will create an `ACCESS EXCLUSIVE` to view how this lock can block all other
queries on a table.

```ruby
ActiveRecord::Base.transaction do
  ActiveRecord::Migration.add_column :toys, :countz, :integer
  sleep(3600) # wait for an hour
end
```

This migration creates an `ALTER TABLE` command that will acquire an `ACCESS EXCLUSIVE`
lock on the `toys` table.

This lock will block any other attempts to acquire a lock on the `toys` table. Allowing
us to queue up queries that we will see in the `pg_locks`.

Below are examples of other locks you can create.

### Row Share Lock

```ruby
Toy.find_by_sql("SELECT * FROM toys WHERE name = 'racecar' FOR UPDATE")
```

### Row Exclusive Lock

```ruby
toy = Toy.find_by(name: 'racecar')
toy.update(person_id: 1)
toy
```

### Share

```ruby
ActiveRecord::Migration.add_index(:toys, :name)
```

## Summary

Locks exist to protect data integrity between concurrent transactions. They allow
us ensure that a long running query or update will not be corrupted by a conflicting
change to the table(s) you are using.

While reading from a table will not block other reads you must be mindful of when
you are planning on running migrations that alter table that are read by multiple
users at once. Specially if those tables require a user to look a multiple records
in one query. An `ALTER TABLE` can easily block a user from reading a record.