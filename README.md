## Cassandra Ingest from Relational Databases with StreamSets

How do you ingest from an existing relational database (RDBMS) to Cassandra?  Or, how about ingesting from existing RDBMS based app to a Kerberized DataStax cluster?  

How do batch load historical data vs stream future changes?

I know what some of you are thinking, no big deal, write and deploy some code.  And maybe the code can use a framework like Apache Spark!  That's what I would have thought a few years ago. But, it often turns out that's not as easy as you'd might expect.  Don't get me wrong, writing code makes much sense for some.  But for others, writing and deploying custom code may require a significant time and resources.  

Are there any alternatives for Cassandra ingestion from a RDBMS?

For example, are there any third party tools available which focus on data ingestion?  And if so,  to Cassandra to RDBMS?  

Yes.  StreamSets Data Collector and StreamSets Control Hub.  In this tutorial, I'll show how you can use the open source StreamSets Data Collector for migrating for an existing RDBMS to Cassandra.


<todo insert screenshot or animated gif>
![Cassandra Ingest with StreamSets](images/cassandra-ingest-intro.gif)

We're going to cover the both **_batch_** and **_streaming_** based  data ingestion from RDBMS to Cassandra:

* Use Case 1: Initial Bulk Load of historical RDBMS based data into Cassandra (batch)

* Use Case 2: Change Data Capture (aka CDC) trickle feed from RDBMS to Cassandra to keep Cassandra updated (streaming)

Why this matters?

* May allows migration to Cassandra more quickly than writing a custom code solution

* Allows you to build confidence in your Cassandra operations using real-world data

* Allows you to switch-over from RDBMS based environment to Cassandra with minimum downtown (in fact, may not be any downtime)

* Utilizes a tool built for ingest, so you can focus on your business objectives.  You're not in the data ingest business, right?  So why build something when you don't have to.


## Our Approach

I'm going to riff off the infamous Killrvideo based references.  In this tutorial, we're going to present and solve for a migrating a RDBMS called Killrmovies to Cassandra.  Killrmovies?  Yeah, I know, I know, not the most original name.  I'm open to other ideas and considered alternatives such as SweetMovies, KickButtVideos and DopeVids, but we're going with Killrmovies for now.  

Killrmovies is a subset of Killrvideo schema and will work well when highlighting the differences in data models.  

In particular, the Killrmovies RDBMS data model is traditional 3NF where normalization is paramount.

// TODO diagram

Conversely, when moving to Cassandra, our data model is based on known queries with denormalized data.  

// TODO chebotko diagram    

We'll utilize Killrvideo because it's often used when introducing and teaching data modeling in Cassandra.  This especially matters for this tutorial where we are taking an existing RDBMS based model and migrating to Cassandra.

I'm assuming you know Killrvideo!  If not, just search for it.  


### Requirements

1. Cassandra or DataStax Enterprise (DSE)
2. RDBMS with JDBC driver such as Oracle, Microsft SQL Server, PostgreSQL or mySQL.  (this tutorial uses mySQL.  swap as necessary.)
3. StreamSets Data Collector
4. mySQL JDBC driver configured for StreamSets origins (shown in screencasts below)

_Please note_: If you are new to StreamSets, you are encouraged to visit http://www.streamsets.com to learn more and complete the Basic Tutorials available at https://streamsets.com/tutorials/ before attempting this tutorial.



### Use Case 1 - Bulk Loading Cassandra from RDBMS

Let's work backwards.  What does the end result of bulk loading into Cassandra from a RDBMS with StreamSets look like?

In the following screencast, I demonstrate how to run provided StreamSets pipeline.  Along the way, we'll review the before and after state of the mySQL database and Cassandra.

// TODO Screencast link
// Show JDBC driver setup

#### Key Deliverables

In this demonstration, we saw the ability to move from a data model appropriate for RDBMS to data model appropriate for Cassandra without writing any code. Instead we utilized StreamSets configuration as code.  That's an important point.  StreamSets Data Collector is not a code generator.  It is design tool and execution engine.  As we saw in the pipeline import, pipelines are represented in JSON config.  

**_Bonus Points_** In addition, we addressed auto-incrementing primary keys in RDBMS to `uuid` fields in Cassandra since auto-incrementing keys do not have a place in distributed databases.

#### StreamSets configuration
// TODO breakdown each stage








### Use Case 2 - Change Data Capture to Cassandra

In databases, change data capture (CDC) is a set of software design patterns used to determine (and track) the data that has changed so that action can be taken using the changed data. [1]

In other words, for this tutorial, a mySQL CDC origin will allows us to determine the insert, update or delete mutations in near real-time and mimic these operations into the Cassandra destination.

StreamSets has many options for CDC databases out-of-the-box including Oracle, Microsoft SQL Server, Mongo, PostgreSQL and mySQL. [2]

When implementing CDC patterns, you usually start with a few high-level choices:

* Do you want to perform logical or physical deletes from origin to destination?  Depends on design of origin.  Logical deletes are often referred to as "soft deletes" where a column is updated to indicate if a record has been deleted or not; i.e. a boolean `deleted` column or an `operation_type` column indicator.

* Do you want to perform logical or physical updates?  When an update happens at the source RDBMS, do you want to update the record in the destatination or do you want to perform write a new record with a logical update.  As an example of logical update, you may have a data model with an `operation_type` column with int value indicators for INSERT, UPDATE or DELETE.  An `operation_type` column is often paired with with a `created_at` timestamp column.  These two columns would allow you to have a running history of updates to a particular entity.  (or even deletes if you choose)

In this tutorial, we're going to implement the logical instead of phyical approach when collecting mutations to Cassandra destination.

In this tutorial, we'll retain history.  See FAQ below for alternative.


// TODO Screencast link
// Import the pipeline
// Show JDBC driver setup

#### Key Deliverables

In this second data pipeline example, we showed how implement CDC from mySQL to Cassandra using a **_streaming_** pipeline.  As opposed to the first example which was a one-time **_batch_** pipeline, this pipeline is intended to run continuously.  

We chose the approach or logical updates and deletes.

#### StreamSets Configuration
// TODO Link to mySQL CDC setup and other options
// maybe a screencast to cover




#### FAQ / Common Objections

1. My RDBMS has too many tables to address in a StreamSets pipeline.

Solution: Break up into multiple pipelines and filter accordingly.  You can filter in both the Bulk Ingest pipeline as well as the CDC origin pipeline:

// TODO show screenshots with filtering option and cdc option to have diff consumer

2. What if I want to perform physical deletes vs. logical?
// TODO

3. What if I want to perform physical updates vs. logical?
In this case, simply update your StreamSets pipeline and Cassandra data model to remove the `created_at` and `operation_type` fields. An existing record in Cassandra will be updated (upsert).  The `cassandra_schema_no_history.cql` file has this model all ready for you.  Note: you'll need to address deletes or sdc.operation.type == 2 in your pipeline with this model.



#### References

[1] https://en.wikipedia.org/wiki/Change_data_capture
[2] https://streamsets.com/documentation/datacollector/3.4.0/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html?hl=cdc



.







.

### Scratch pad now - Remove following

// Setup notes
Cassandra running
Start cassandra `bin/cassandra -f`
bin/cqlsh -f ~/dev/cassandra-ingest/schema/cassandra_schema.cql

Add mySQL steps

-- select t.*, r.* from tags t cross join ratings r on t.user_id = r.user_id where t.user_id = 1


Notes

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
