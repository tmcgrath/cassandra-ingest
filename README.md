# Cassandra Ingest from Relational Databases

How do you ingest from an existing relational database (RDBMS) to Cassandra?  Or, how about ingesting from existing RDBMS based app to a Kerberized DataStax cluster?  

How do batch load historical data vs stream future changes?

I know what some of you are thinking, no big deal, write and deploy some code.  And maybe the code can use a framework like Apache Spark!  That's what I would have thought a few years ago. But, it often turns out that's not as easy as you'd might expect.  Don't get me wrong, writing code makes much sense for some.  But for others, writing and deploying custom code may require a significant time and resources.  

Are there any alternatives for Cassandra ingestion from a RDBMS?

For example, are there any third party tools available which focus on data ingestion?  And if so,  to Cassandra to RDBMS?  

Yes.  StreamSets Data Collector and StreamSets Control Hub.  In this tutorial, I'll show how you can use the open source StreamSets Data Collector for migrating for an existing RDBMS to Cassandra.


<todo insert screenshot or animated gif>
![Cassandra Ingest with StreamSets](./images/cassandra-ingest-intro.gif)

We're going to cover the both **_batch_** and **_streaming_** based  data ingestion from RDBMS to Cassandra:

* Use Case 1: Initial Bulk Load of historical RDBMS based data into Cassandra (batch)

* Use Case 2: Change Data Capture (aka CDC) trickle feed from RDBMS to Cassandra to keep Cassandra updated (streaming)

Why does this matter?

* May allows migration to Cassandra more quickly than writing a custom code solution

* Allows you to build confidence in your Cassandra operations using real-world data

* Allows you to switch-over from RDBMS based environment to Cassandra with minimum downtown (in fact, may not be any downtime)

* Utilizes a tool built for ingest, so you can focus on your business objectives.  You're not in the data ingest business, right?  So why build something when you don't have to.


# Our Approach

I'm going to riff off the infamous Killrvideo references.  I'm assuming you know Killrvideo!  If not, just search for it.  

We'll utilize Killrvideo because it's often used when introducing and teaching data modeling in Cassandra.  This especially matters for this tutorial where we are taking an existing RDBMS based model and migrating to Cassandra.  

In the following examples, we're going to present and solve for a migrating a RDBMS called Killrmovies to Cassandra.  Killrmovies?  I know, I know, not the most original name and I'm open to other ideas.  Considered alternatives were SweetMovies, KickButtVideos and DopeVids, but we're going with Killrmovies for now.  Killrmovies is a subset of Killrvideo schema.   


## Requirements

1. Cassandra or DataStax Enterprise (DSE)
2. RDBMS with JDBC driver such as Oracle, Microsft SQL Server, PostgreSQL or mySQL.  (this tutorial uses mySQL.  swap as necessary.)
3. StreamSets Data Collector

//TODO - add note about being new SDC, then follow the Getting Started Tutorials.

## Use Case 1 - Bulk Loading Cassandra from RDBMS

Let's work backwards.  What does the end result of bulk loading into Cassandra from a RDBMS with StreamSets look like?

In the following screencast, I demonstrate how to run provided StreamSets pipeline.  Along the way, we'll review the before and after state of the mySQL database and Cassandra.

// TODO Screencast link


## Key Deliverables

In this demonstration, we saw the ability to move from a data model appropriate for RDBMS to data model appropriate for Cassandra without writing any code. Instead we utilized StreamSets configuration as code.  That's an important point.  StreamSets Data Collector is not a code generator.  It is design tool and execution engine.  As we saw in the pipeline import, pipelines are represented in JSON config.  

## StreamSets configuration
// TODO breakdown each stage








## Use Case 2 - Change Data Capture to Cassandra

// TODO - add background on what CDC is.  then show screencast demo

## Key Deliverables

// TODO

## StreamSets Configuration

## Cassandra configuration
// TODO - TBD - add field for `operation.type`






## Scratch pad now - Remove following

// Setup notes
Cassandra running
Start cassandra `bin/cassandra -f`
bin/cqlsh -f ~/dev/cassandra-ingest/schema/cassandra_schema.cql

Add mySQL steps

-- select t.*, r.* from tags t cross join ratings r on t.user_id = r.user_id where t.user_id = 1

// Maybe Dockerize this ^

* RDMBS to Cassandra Bulk Load

Notes
* Create video of running it and then describe each stage

* Multi-table is filtering for only users and movies tables

* about auto-increment doesn't work well with distributed db like cassandra so create mapping tables in source db for bulk load lookups, so we implemented a UUID mapping lookup in the database.

* JDBC Lookups and first only vs. split

* Cassandra Destination call out Auth Provider and Protocol version options

* Show History after the pipeline stops




//TODO Notes & Improvement Ideas

* Docker container with all of this and using DSE instead

* We could also do CDC to Kafka and Kafka to Cassandra with StreamSets

* Is this true anymore -> After bulk load which may have a ton of tombstones?  Whadda do?

* Is this true anymore -> When a movie has more than one tag, the bulk pipeline will upsert into movies destination table and cause a tombstone.  Could we prevent this somehow?  See previous point


* Maybe add more content like preview_image, location, etc.

* Maybe add Redis or caching lookup before using JDBC Lookup



// Why StreamSets?
Key value is being able to load into multiple destination tables.  This is different than cassandra-loader and DSBulk where there appears to be one-to-one from source to table destination.  The alternative is to use Spark to process and split source, but this requires a certain type of dev and operations skillset.  StreamSets fits somewhere in between
