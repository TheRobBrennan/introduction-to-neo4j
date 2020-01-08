# Welcome

This folder has been created to track my work and ideas while studying [Getting More Out of Neo4j](https://neo4j.com/graphacademy/online-training/introduction-to-neo4j/part-7/)

For my own learning purposes, I will include sections of text from the original course to solidify my own understanding of the concepts presented.

## About this module

You have learned how to set up your development environment for accessing a Neo4j graph and how to write basic Cypher statements for querying the graph modifying the graph.

At the end of this module, you should be able to:

+ Use parameters in your Cypher statements.
+ Analyze Cypher execution.
+ Monitor queries.
+ Manage constraints and node keys for the graph.
+ Import data into a graph from CSV files.
+ Manage indexes for the graph.
+ Access Neo4j resources.

## Cypher parameters

In a deployed application, you should not hard code values in your Cypher statements. You use a variety values when you are testing your Cypher statements. But you don’t want to change the Cypher statement every time you test. In addition, you typically include Cypher statements in an application where parameters are passed in to the Cypher statement before it executes. For these scenarios, you should parameterize values in your Cypher statements.

### Using Cypher parameters

In your Cypher statements, a parameter name begins with the $ symbol.

Here is an example where we have parameterized the query:

```javascript
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name = $actorName
RETURN m.released, m.title ORDER BY m.released DESC
```

At runtime, if the parameter $actorName has a value, it will be used in the Cypher statement when it runs in the graph engine.

In Neo4j Browser, you can set values for Cypher parameters that will be in effect during your session.

You can set the value of a single parameter in the query editor pane as shown in this example where the value Tom Hanks is set for the parameter actorName:

```javascript
:param actorName => 'Tom Hanks'
```

You can even specify a Cypher expression to the right of => to set the value of the parameter.

Notice here that :param is a client-side browser command. It takes a name and expression and stores the value of that expression for the name in the session.

After the actorName parameter is set, you can run the query that uses the parameter.

Subsequently, you need only change the value of the parameter and not the Cypher statement to test with different values.

You can also use the JSON-style syntax to set all of the parameters in your Neo4j Browser session. The values you can specify in this object are numbers, strings, and booleans. In this example we set two parameters for our session:

```javascript
:params {actorName: 'Tom Cruise', movieName: 'Top Gun'}
```

If you want to remove an existing parameter from your session, you do by by using the JSON-style syntax and excluding the parameter for your session.

If you want to view the current parameters and their values, simply type `:params`

### Exercise 12: Using Cypher parameters

In the query edit pane of Neo4j Browser, execute the browser command: :play intro-neo4j-exercises and follow the instructions for Exercise 12.

```javascript
// Exercise 12.1: Execute a Cypher query as described.
// Write and execute a Cypher query that returns the names of people who reviewed movies and the actors in these movies by returning the name of the reviewer, the movie title reviewed, the release date of the movie, the rating given to the movie by the reviewer, and the list of actors for that particular movie.
MATCH (r:Person)-[rel:REVIEWED]->(m:Movie)<-[:ACTED_IN]-(a:Person)
RETURN  DISTINCT r.name, m.title, m.released, rel.rating, collect(a.name)

// Exercise 12.2: Add a parameter to your session.
:param year => 2000

// Exercise 12.3: Modify the Cypher query you just wrote to use a parameter.
MATCH (r:Person)-[rel:REVIEWED]->(m:Movie)<-[:ACTED_IN]-(a:Person)
WHERE m.released = $year
RETURN  DISTINCT r.name, m.title, m.released, rel.rating, collect(a.name)

// Exercise 12.4: Modify parameter value and retest your query.
:param year => 2006

// Exercise 12.5: Add a different parameter to your session.
:params {year: 2006, ratingValue: 65}

// Exercise 12.6: Modify the query you wrote previously to use the second parameter.
MATCH (r:Person)-[rel:REVIEWED]->(m:Movie)<-[:ACTED_IN]-(a:Person)
WHERE m.released = $year AND
      rel.rating > $ratingValue
RETURN  DISTINCT r.name, m.title, m.released, rel.rating, collect(a.name)

// Exercise 12.7: Modify the second parameter value and retest your query.
:params {year: 2006, ratingValue: 60}
```

## Analyzing Cypher execution

The Movie graph that you have been using during training is a very small graph. As you start working with large datasets, it will be important to not only add appropriate indexes to your graph, but also write Cypher statements that execute as efficiently as possible.

There are two Cypher keywords you can prefix a Cypher statement with to analyze a query:

+ EXPLAIN provides estimates of the graph engine processing that will occur, but does not execute the Cypher statement.
+ PROFILE provides real profiling information for what has occurred in the graph engine during the query and executes the Cypher statement.

The EXPLAIN option provides the Cypher query plan. You can compare different Cypher statements to understand the stages of processing that will occur when the Cypher executes.

Here is an example where we have set the actorName and year parameters for our session and we execute this Cypher statement:

```javascript
EXPLAIN MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name = $actorName AND
      m.released <  $year
RETURN p.name, m.title, m.released
```

Here is the query plan returned:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/EXPLAIN.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/EXPLAIN.png)

You can expand each phase of the Cypher execution to examine what code is expected to run. Each phase of the query presents you with an estimate of the number of rows expected to be returned. With EXPLAIN, the query does not run, the graph engine simply produces the query plan.

For a better metric for analyzing how the Cypher statement will run you use the PROFILE keyword which runs the Cypher statement and gives you run-time performance metrics.

Here is the result returned using PROFILE for this Cypher statement:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/PROFILE1.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/PROFILE1.png)

Here we see that for each phase of the graph engine processing, we can view the cache hits and most importantly the number of times the graph engine accessed the database (db hits). This is an important metric that will affect the performance of the Cypher statement at run-time.

For example, if we were to change the Cypher statement so that the node labels are not specified, we see these metrics when we profile:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/PROFILE2.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/PROFILE2.png)

Here we see more db hits which makes sense because all nodes need to be scanned for perform this query.

## Monitoring queries

If you are testing an application and have run several queries against the graph, there may be times when your Neo4j Browser session hangs with what seems to be a very long-running query. There are two reasons why a Cypher query may take a long time:

+ The query returns a lot of data. The query completes execution in the graph engine, but it takes a long time to create the result stream.
  + Example: MATCH (a)--(b)--(c)--(d)--(e)--(f) RETURN a
+ The query takes a long time to execute in the graph engine.
  + Example: MATCH (a), (b), (c), (d), (e) RETURN count(id(a))

If the query executes and then returns a lot of data, there is no way to monitor it or kill the query. All that you can do is close your Neo4j Browser session and start a new one. If the server has many of these rogue queries running, it will slow down considerably so you should aim to limit these types of queries. If you are running Neo4j Desktop, you can simply restart the database to clear things up, but if you are using a Neo4j Sandbox, you cannot do so. The database server is always running and you cannot restart it. Your only option is to shut down the Neo4j Sandbox and create a new Neo4j Sandbox, but then you lose any data you have worked with.

If, however, the query is a long-running query, you can monitor it by using the :queries command. Here is a screenshot where we are monitoring a long-running query in another Neo4j Browser session:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/ListQueries.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/ListQueries.png)

The `:queries` command calls `dbms.listQueries` under the hood, which is why we see two queries here. We have turned on AUTO-REFRESH so we can monitor the number of ms used by the graph engine thus far. You can kill the running query by double-clicking the icon in the Kill column. Alternatively, you can execute the statement CALL `dbms.killQuery('query-id')`.

NOTE The :queries command is only available in the Enterprise Edition of Neo4j.

### Exercise 13: Analyzing and monitoring queries

In the query edit pane of Neo4j Browser, execute the browser command: :play intro-neo4j-exercises and follow the instructions for Exercise 13.

```javascript
// Exercise 13.1: View the query plan for a Cypher statement.
EXPLAIN MATCH (r:Person)-[rel:REVIEWED]->(m:Movie)<-[:ACTED_IN]-(a:Person)
WHERE m.released = $year AND
      rel.rating > $ratingValue
RETURN  DISTINCT r.name, m.title, m.released, rel.rating, collect(a.name)

// Exercise 13.2: View the metrics for the query when a statement executes.
PROFILE MATCH (r:Person)-[rel:REVIEWED]->(m:Movie)<-[:ACTED_IN]-(a:Person)
WHERE m.released = $year AND
      rel.rating > $ratingValue
RETURN  DISTINCT r.name, m.title, m.released, rel.rating, collect(a.name)

// Exercise 13.3: Remove the labels from the nodes and relationships in the query and again view the metrics.
PROFILE MATCH (r)-[rel]->(m)<-[:ACTED_IN]-(a)
WHERE m.released = $year AND
      rel.rating > $ratingValue
RETURN  DISTINCT r.name, m.title, m.released, rel.rating, collect(a.name)

// Exercise 13.4: Open a second Neo4j Browser session.
// Exercise 13.5: Execute a long-running query and monitor.
// Exercise 13.6: Execute another long-running query and monitor.
// Exercise 13.7: Kill a query.
```

## Managing constraints and node keys

### Ensuring that a property value for a node is unique

### Ensuring that properties exist

### Retrieving constraints defined for the graph

### Dropping constraints

### Creating node keys

### Exercise 14: Managing constraints and node keys

## Managing indexes

### Indexes for range searches

### Creating indexes

### Retrieving indexes

### Dropping indexes

### Exercise 15: Managing indexes

## Going from relational to graph with Neo4j

## Importing data

### Importing normalized data using LOAD CSV

### Importing denormalized data

### Importing a large dataset

### Exercise 16: Importing data

## Accessing Neo4j resources

There are many ways that you can learn more about Neo4j. A good starting point for learning about the resources available to you is the Neo4j Learning Resources page at [https://neo4j.com/developer/resources/](https://neo4j.com/developer/resources/).

## Check your understanding

### Question 1

What Cypher keyword can you use to prefix any Cypher statement to examine how many db hits occurred when the statement executed?

Select the correct answer.

[ ] ANALYZE
[ ] EXPLAIN
[ ] PROFILE
[ ] MONITOR

### Question 2

What types of constraints can you define for a graph that are asserted when a node or relationship is created or updated?

Select the correct answers.

[ ] unique values for a property of a node
[ ] unique values for a property of a relationship
[ ] a node must have a certain set of properties with values
[ ] a relationship must have a certain set of properties with values

### Question 3

In general, what is the maximum number of nodes or relationships that you can easily create using LOAD CSV?

Select the correct answer.

[ ] 1K
[ ] 10K
[ ] 1M
[ ] 10M

## Summary

You should now be able to:

+ Use parameters in your Cypher statements.
+ Analyze Cypher execution.
+ Monitor queries.
+ Manage constraints and node keys for the graph.
+ Import data into a graph from CSV files.
+ Manage indexes for the graph.
+ Access Neo4j resources.
