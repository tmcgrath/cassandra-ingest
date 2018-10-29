# cassandra-ingest
WIP

# Overview
There are many ways to implement RDBMS to Cassandra Ingest using StreamSets.  We're going to go through a few examples in this
repo.  We'll cover two patterns: 1) Initial historical bulk load and 2) Change Data Capture (CDC) trickle feeds

In these examples, we're going to riff off the infamous Killrvideo app and database.  In these examples, we'll present and solve for the use case where we built a site called Killrmovies in an RDBMS but we want to move to Cassandra for various reasons.  How could we do that?

//TODO Notes

* After bulk load which may have a ton of tombstones?  Whadda do

* Maybe add more content like preview_image, location, etc.

* Maybe add Redis or caching lookup before using JDBC Lookup

* Maybe add `cdc.type` field to each table (or have that has a part 2).  Need to research appropriate cassandra data model for this


// Requirements
Cassandra, mySQL (any JDBC db such as Oracle, MS SQL, Postgres), StreamSets Data Collector

// Setup notes
Cassandra running
Start cassandra `bin/cassandra -f`
bin/cqlsh -f ~/dev/cassandra-ingest/schema/cassandra_schema.cql

Add mySQL steps

-- select t.*, r.* from tags t cross join ratings r on t.user_id = r.user_id where t.user_id = 1

// Maybe Dockerize this ^ 

* RDMBS to Cassandra Bulk Load

Note about auto-increment doesn't work well with distributed db like cassandra so create mapping tables in source db for bulk load lookups, so we implemented a GUID mapping lookup in the database.


