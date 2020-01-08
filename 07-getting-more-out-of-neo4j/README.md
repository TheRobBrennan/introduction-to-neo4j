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

## Monitoring queries

### Exercise 13: Analyzing and monitoring queries

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
