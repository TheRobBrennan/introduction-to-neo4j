# Welcome

This folder has been created to track my work and ideas while studying [Introduction to Cypher](https://neo4j.com/graphacademy/online-training/introduction-to-neo4j/part-4/)

For my own learning purposes, I will include sections of text from the original course to solidify my own understanding of the concepts presented.

## About this module

Cypher is the query language you use to retrieve data from the Neo4j Database, as well as create and update the data.

At the end of this module, you should be able to write Cypher statements to:

+ Retrieve nodes from the graph.
+ Filter nodes retrieved using labels and property values of nodes.
+ Retrieve property values from nodes in the graph.
+ Filter nodes retrieved using relationships.

Throughout this training, you should refer to:

+ [Neo4j Cypher Manual](https://neo4j.com/docs/cypher-manual/current/)
+ [Cypher Reference card](https://neo4j.com/docs/cypher-refcard/current/)

## What is Cypher?

Cypher is a declarative query language that allows for expressive and efficient querying and updating of graph data. Cypher is a relatively simple and very powerful language. Complex database queries can easily be expressed through Cypher, allowing you to focus on your domain instead of getting lost in the syntax of database access.

Cypher is designed to be a human-friendly query language, suitable for both developers and other professionals. The guiding goal is to make the simple things easy, and the complex things possible.

### Cypher is ASCII art

Optimized for being read by humans, Cypher’s construct uses English prose and iconography (called ASCII Art) to make queries more self-explanatory.

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/AsciiArt.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/AsciiArt.png)

Being a declarative language, Cypher focuses on the clarity of expressing what to retrieve from a graph, not on how to retrieve it. You can think of Cypher as mapping English language sentence structure to patterns in a graph. For example, the nouns are nodes of the graph, the verbs are the relationships in the graph, and the adjectives and adverbs are the properties.

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/Nouns.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/Nouns.png)

This is in contrast to imperative, programmatic APIs for database access. This approach makes query optimization an implementation detail instead of a burden on the developer, removing the requirement to update all traversals just because the physical database structure has changed.

### Nodes

Cypher uses a pair of parentheses like `()`, `(n)` to represent a node, much like a circle on a whiteboard. Recall that a node typically represents an entity in your domain. An anonymous node, `()`, represents one or more nodes during a query processing where there are no restrictions of the type of node or the properties of the node. When you specify `(n)` for a node, you are telling the query processor that for this query, use the variable `n` to represent nodes that will be processed later in the query for further query processing or for returning values from the query.

### Labels

Nodes in a graph are typically labeled. Labels are used to group nodes and filter queries against the graph. That is, labels can be used to optimize queries.

In the Movie database you will be working with, the nodes in this graph are labeled `Movie` or `Person` to represent two types of nodes.

You can filter the types of nodes that you are querying, by specifying a label for a node. A node can have zero or more labels.

Here are simplified syntax examples for specifying a node:

```javascript
()
(variable)
(:Label)
(variable:Label)
(:Label1:Label2)
(variable:Label1:Label2)
```

Notice that a node must have the parentheses. The labels and the variable for a node are optional.

Here are examples of specifying nodes in Cypher:

```javascript
()                  // anonymous node not be referenced later in the query
(p)                 // variable p, a reference to a node used later
(:Person)           // anonymous node of type Person
(p:Person)          // p, a reference to a node of type Person
(p:Actor:Director)  // p, a reference to a node of types Actor and Director
```

A node can have multiple labels. For example a node can be created with a label of `Person` and that same node can be modified to also have the label of `Actor` and/or `Director`.

### Comments in Cypher

In Cypher, you can place a comment (starts with `//`) anywhere in your Cypher to specify that the rest of the line is interpreted as a comment.

## Examining the data model

When you are first learning about the data (nodes, labels, etc.) in a graph, it is helpful to examine the data model of the graph. You do so by executing `CALL db.schema`, which calls the Neo4j procedure that returns information about the nodes, labels, and relationships in the graph.

Using an example Movies database, we can see a simplified view of how our nodes, labels, and relationships relate to each other. In the example below, our graph database actually contains 171 nodes - with `Movie` or `Person` labels - and 253 relationships (with six relationship types defined)

![00.png](00.png)

By running `CALL db.schema`, we can see:

+ A Person can follow another Person
+ A Person can have a relationship with a Movie
  + ACTED_IN
  + DIRECTED
  + FOLLOWS
  + PRODUCED
  + REVIEWED
  + WROTE

## Using MATCH to retrieve nodes

[WATCH: How to execute a MATCH statement in Neo4j Browser ~2:33](https://www.youtube.com/watch?v=Sz2C618QKN8)

The most widely used Cypher clause is `MATCH`. The `MATCH` clause performs a pattern match against the data in the graph. During the query processing, the graph engine traverses the graph to find all nodes that match the graph pattern. As part of query, you can return nodes or data from the nodes using the `RETURN` clause. The `RETURN` clause must be the last clause of a query to the graph. Later in this training, you will learn how to use `MATCH` to select nodes and data for updating the graph. First, you will learn how to simply return nodes.

Notice that the Cypher keywords MATCH and RETURN are upper-case. This coding convention is described in the Cypher Style Guide and will be used in this training. This MATCH clause returns all nodes in the graph, where the optional Label is used to return a subgraph if the graph contains nodes of different types. The variable must be specified here, otherwise the query will have nothing to return.

Here are example queries to the Movie database:

```javascript
MATCH (n)           // returns all nodes in the graph
RETURN n

MATCH (p:Person)    // returns all Person nodes in the graph
RETURN p
```

**NOTE: When you specify a pattern for a MATCH clause, you should always specify a node label if possible. In doing so, the graph engine uses an index to retrieve the nodes which will perform better than not using a label for the MATCH.**

### Viewing nodes as table data

We can also view the nodes as table data where the nodes and their associated property values are shown in a JSON-style format.

## Exercise 1: Retrieving nodes

In the query edit pane of Neo4j Browser, execute the browser command: `:play intro-neo4j-exercises` and follow the instructions for Exercise 1.

```javascript
// Exercise 1.1: Retrieve all nodes from the database (Solution)
MATCH (n) RETURN n

// Exercise 1.2: Examine the schema of your database (Solution)
CALL db.schema()

// Exercise 1.3: Retrieve all Person nodes (Solution)
MATCH (p:Person) RETURN p

// Exercise 1.4: Retrieve all Movie nodes (Solution)
MATCH (p:Movie) RETURN p
```

## Properties

In Neo4j, a node (and a relationship, which you will learn about later) can have properties that are used for further define a node. A property is identified by its property key. Recall that nodes are used to represent the entities of your business model. A property is defined for a node and not for a type of node. All nodes of the same type need not have the same properties.

For example, in the Movie graph, all Movie nodes have both title and released properties. However, it is not a requirement that every Movie node has a property, tagline.

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/MovieProperties.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/MovieProperties.png)

Properties can be used to filter queries so that a subset of the graph is retrieved. In addition, with the RETURN clause, you can return property values from the retrieved nodes, rather than the nodes.

### Examining property keys

As you prepare to create Cypher queries that use property values to filter a query, you can view the values for property keys of a graph by simply clicking the Database icon in Neo4j Browser. Alternatively, you can execute `CALL db.propertyKeys`, which calls the Neo4j library method that returns the property keys for the graph.

Here is what you will see in the result pane when you call the method to return the property keys in the Movie graph. This result stream contains all property keys in the graph. It does not display which nodes utilize these property keys.

### Retrieving nodes filtered by a property value

You have learned previously that you can filter node retrieval by specifying a label. Another way you can filter a retrieval is to specify a value for a property. Any node that matches the value will be retrieved.

Here are simplified syntax examples for a query where we specify one or more values for properties that will be used to filter the query results and return a subset of the graph:

```javascript
MATCH (variable {propertyKey: propertyValue})
RETURN variable

MATCH (variable:Label {propertyKey: propertyValue})
RETURN variable

MATCH (variable {propertyKey1: propertyValue1, propertyKey2: propertyValue2})
RETURN variable

MATCH (variable:Label {propertyKey: propertyValue, propertyKey2: propertyValue2})
RETURN variable
```

Here is an example where we filter the query results using a property value. We only retrieve Person nodes that have a born property value of 1970:

```javascript
MATCH (p:Person {born: 1970})
RETURN p
```

Here is an example where we specify two property values for the query:

```javascript
MATCH (m:Movie {released: 2003, tagline: 'Free your mind'})
RETURN m
```

### Returning property values

In this video, you will see how to return property values to the output stream when you retrieve nodes from the graph in Neo4j Browser:

[WATCH: Using MATCH to Return Property Values In Neo4j Browser ~1:46](https://www.youtube.com/watch?v=Nb9tSFVrQuc)

Here are simplified syntax examples for returning property values, rather than nodes:

```javascript
MATCH (variable {prop1: value})
RETURN variable.prop2

MATCH (variable:Label {prop1: value})
RETURN variable.prop2

MATCH (variable:Label {prop1: value, prop2: value})
RETURN variable.prop3

MATCH (variable {prop1:value})
RETURN variable.prop2, variable.prop3
```

In this example, we use the born property to refine the query, but rather than returning the nodes, we return the name and born values for every node that satisfies the query:

```javascript
MATCH (p:Person {born: 1965})
RETURN p.name, p.born
```

This gives us a table of results like:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/MatchPersonBorn1965.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/MatchPersonBorn1965.png)

### Specifying aliases

If you want to customize the headings for a table containing property values, you can specify aliases for column headers.

Here is the simplified syntax for specifying an alias for a property value:

```javascript
MATCH (variable:Label {propertyKey1: propertyValue1})
RETURN variable.propertyKey2 AS alias2
```

**NOTE: If you want a heading to contain a space between strings, you must specify the alias with the back tick character, rather than a single or double quote character. In fact, you can specify any variable, label, relationship type, or property key with a space also by using the back tick ` character.**

Here we specify aliases for the returned property values:

```javascript
MATCH (p:Person {born: 1965})
RETURN p.name AS name, p.born AS `birth year`
```

## Exercise 2: Filtering queries using property values

In the query edit pane of Neo4j Browser, execute the browser command: `:play intro-neo4j-exercises` and follow the instructions for Exercise 2.

```javascript
// Exercise 2.1: Retrieve all movies that were released in a specific year (Solution)
MATCH (m:Movie {released:2003}) RETURN m

// Exercise 2.2: View the retrieved results as a table (Solution)

// Exercise 2.3: Query the database for all property keys (Solution)
CALL db.propertyKeys

// Exercise 2.4: Retrieve all Movies released in a specific year, returning their titles (Solution)
MATCH (m:Movie {released: 2006}) RETURN m.title

// Exercise 2.5: Display title, released, and tagline values for every Movie node in the graph (Solution)
MATCH (m:Movie) RETURN m.title, m.released, m.tagline

// Exercise 2.6: Display more user-friendly headers in the table (Solution)
MATCH (m:Movie) RETURN m.title AS `movie title`, m.released AS released, m.tagline AS tagLine
```

## Relationships

Relationships are what make Neo4j graphs such a powerful tool for connecting complex and deep data. A relationship is a directed connection between two nodes that has a relationship type (name). In addition, a relationship can have properties, just like nodes. In a graph where you want to retrieve nodes, you can use relationships between nodes to filter a query.

### ASCII art

Thus far, you have learned how to specify a node in a MATCH clause. You can specify nodes and their relationships to traverse the graph and quickly find the data of interest.

Here is how Cypher uses ASCII art to specify path used for a query:

```javascript
()          // a node
()--()      // 2 nodes have some type of relationship
()-->()     // the first node has a relationship to the second node
()<--()     // the second node has a relationship to the first node
```

### Querying using relationships

In your MATCH clause, you specify how you want a relationship to be used to perform the query. The relationship can be specified with or without direction.

Here are simplified syntax examples for retrieving a set of nodes that satisfy one or more directed and typed relationships:

```javascript
MATCH (node1)-[:REL_TYPE]->(node2)
RETURN node1, node2

MATCH (node1)-[:REL_TYPEA \| :REL_TYPEB]->(node2)
RETURN node1, node2

node1
:REL_TYPE                 // relationship from node1 to node2
:REL_TYPEA , :REL_TYPEB   // relationships from node1 to node2 where one or both exists
node2
```

### Examining relationships

You can run `CALL db.schema` to view the relationship types in the graph.

In the Movie graph we see that this graph has a total of 6 relationship types between the nodes. Some `Person` nodes are connected to other `Person` nodes using the `FOLLOWS` relationship type. All of the other relationships in this graph are from `Person` nodes to `Movie` nodes.

### Using a relationship in a query

Here is an example where we retrieve the `Person` nodes that have the `ACTED_IN` relationship to the Movie, `The Matrix`.

In other words, show me the actors that acted in The Matrix:

```javascript
// Show me the actors that acted in The Matrix
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie {title: 'The Matrix'})
RETURN p, rel, m
```

The result returned is:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/ActorsInMatrix.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/ActorsInMatrix.png)

For this query, we are using the variable `p` to represent the `Person` nodes during the query, the variable `m` to represent the `Movie` node retrieved, and the variable `rel` to represent the relationship for the relationship type, `ACTED_IN`. We return a graph with the `Person` nodes, the `Movie` node and their `ACTED_IN` relationships.

Important: You specify node labels whenever possible in your queries as it optimizes the retrieval in the graph engine. That is, you should not specify this same query as:

```javascript
MATCH (p)-[rel:ACTED_IN]->(m {title:'The Matrix'})
RETURN p,m
```

### Querying by multiple relationships

Here is another example where we want to know the movies that Tom Hanks acted in and directed:

```javascript
MATCH (p:Person {name: 'Tom Hanks'})-[:ACTED_IN|:DIRECTED]->(m:Movie)
RETURN p.name, m.title
```

The result returned is:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/TomHanksActedDirected.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/TomHanksActedDirected.png)

Notice that there are multiple rows returned for the movie, `That Thing You Do`. This is because Tom Hanks acted in **AND** directed that movie.

### Using anonymous nodes in a query

Suppose you wanted to retrieve the actors that acted in The Matrix, but you do not need any information returned about the Movie node. You need not specify a variable for a node in a query if that node is not returned or used for later processing in the query. You can simply use the anonymous node in the query as follows:

```javascript
MATCH (p:Person)-[:ACTED_IN]->(:Movie {title: 'The Matrix'})
RETURN p.name
```

The result returned is:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/AnonymousMovieNode.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/AnonymousMovieNode.png)

NOTE	A best practice is to place named nodes (those with variables) before anonymous nodes in a MATCH clause.

### Using an anonymous relationship for a query

Suppose you want to find all people who are in any way connected to the movie, `The Matrix`. You can specify an empty relationship type in the query so that all relationships are traversed and the appropriate results are returned. In this example, we want to retrieve all `Person` nodes that have any type of connection to the `Movie` node, with the title, The Matrix. This query returns more nodes with the relationships types, `DIRECTED`, `ACTED_IN`, and `PRODUCED`.

```javascript
MATCH (p:Person)-->(m:Movie {title: 'The Matrix'})
RETURN p, m
```

The result returned is:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/AllRelationshipsMatrix.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/AllRelationshipsMatrix.png)

Here are other examples of using the anonymous relationship:

```javascript
// Find all Person nodes with any relationship to the movie "The Matrix"
MATCH (p:Person)--(m:Movie {title: 'The Matrix'})
RETURN p, m

// Find all Movie nodes that Keanu Reeves has a relationship with
MATCH (m:Movie)<--(p:Person {name: 'Keanu Reeves'})
RETURN p, m
```

In this training, we will use `-->`, `--`, and `<--` to represent anonymous relationships as it is a Cypher best practice.

### Retrieving the relationship types

There is a built-in function, `type()` that returns the relationship type of a relationship.

Here is an example where we use the rel variable to hold the relationships retrieved. We then use this variable to return the relationship types:

```javascript
MATCH (p:Person)-[rel]->(:Movie {title:'The Matrix'})
RETURN p.name, type(rel)
```

The result returned is:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/MatrixRelationshipTypes.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/MatrixRelationshipTypes.png)

### Retrieving properties for relationships

Recall that a node can have as set of properties, each identified by its property key. Relationships can also have properties. This enables your graph model to provide more data about the relationships between the nodes.

Here is an example from the Movie graph. The movie, The Da Vinci Code has two people that reviewed it, Jessica Thompson and James Thompson. Each of these Person nodes has the REVIEWED relationship to the Movie node for The Da Vinci Code. Each relationship has properties that further describe the relationship using the summary and rating properties.

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/REVIEWEDProperties.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/REVIEWEDProperties.png)

Just as you can specify property values for filtering nodes for a query, you can specify property values for a relationship. This query returns the name of of the person who gave the movie a rating of 65:

```javascript
// Find the Person who gave this movie a rating of 65
MATCH (p:Person)-[:REVIEWED {rating: 65}]->(:Movie {title: 'The Da Vinci Code'})
RETURN p.name
```

## Using patterns for queries

Thus far, you have learned how to specify nodes, properties, and relationships in your Cypher queries. Since relationships are directional, it is important to understand how patterns are used in graph traversal during query execution. How a graph is traversed for a query depends on what directions are defined for relationships and how the pattern is specified in the MATCH clause.

Here is an example of where the FOLLOWS relationship is used in the Movie graph. Notice that this relationship is directional:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/FollowsRelationships.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/FollowsRelationships.png)

We can perform a query that returns all Person nodes who follow Angela Scope:

```javascript
// Which Person nodes are following Angela Scope?
MATCH  (p:Person)-[:FOLLOWS]->(:Person {name:'Angela Scope'})
RETURN p
```

If we reverse the direction in the pattern, the query returns different results:

```javascript
// Which Person nodes are being followed by Angela Scope?
MATCH  (p:Person)<-[:FOLLOWS]-(:Person {name:'Angela Scope'})
RETURN p
```

### Querying by any direction of the relationship

We can also find out what Person nodes are connected by the `FOLLOWS` relationship in either direction by removing the directional arrow from the pattern:

```javascript
// Find all Person nodes that Angela FOLLOWS or Person nodes that FOLLOW Angela
MATCH  (p1:Person)-[:FOLLOWS]-(p2:Person {name:'Angela Scope'})
RETURN p1, p2
```

### Traversing relationships

Since we have a graph, we can traverse through nodes to obtain relationships further into the traversal.

For example, we can write a Cypher query to return all followers of the followers of Jessica Thompson:

```javascript
// Find all Person nodes who have a FOLLOWS relationship to Person nodes that follow a specific Person
MATCH  (p:Person)-[:FOLLOWS]->(:Person)-[:FOLLOWS]->(:Person {name:'Jessica Thompson'})
RETURN p
```

This query could also be modified to return each person along the path by specifying variables for the nodes and returning them. In addition, you can assign a variable to the path and return the path as follows:

```javascript
MATCH  path = (:Person)-[:FOLLOWS]->(:Person)-[:FOLLOWS]->(:Person {name:'Jessica Thompson'})
RETURN  path
```

The result returned is:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/ReturnPath.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/ReturnPath.png)

### Using relationship direction to optimize a query

When querying the relationships in a graph, you can take advantage of the direction of the relationship to traverse the graph. For example, suppose we wanted to get a result stream containing rows of actors and the movies they acted in, along with the director of the particular movie.

Here is the Cypher query to do this. Notice that the direction of the traversal is used to focus on a particular movie during the query:

```javascript
// Find actors that ACTED_IN a movie; also identify who the director of the movie was
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person)
RETURN a.name, m.title, d.name
```

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/TraversalInTwoDirections.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/TraversalInTwoDirections.png)

In this query, notice that there are multiple records returned for a movie, each with its set of values for the actor and director.

Later in this training, you will learn other ways to query data and how to control the results returned.

## Cypher style recommendations

Here are the Neo4j-recommended Cypher coding standards that we use in this training:

+ Node labels are CamelCase and begin with an upper-case letter (examples: Person, NetworkAddress). Note that node labels are case-sensitive.
+ Property keys, variables, parameters, aliases, and functions are camelCase and begin with a lower-case letter (examples: businessAddress, title). Note that these elements are case-sensitive.
+ Relationship types are in upper-case and can use the underscore. (examples: ACTED_IN, FOLLOWS). Note that relationship types are case-sensitive and that you cannot use the “-” character in a relationship type.
+ Cypher keywords are upper-case (examples: MATCH, RETURN). Note that Cypher keywords are case-insensitive, but a best practice is to use upper-case.
+ String constants are in single quotes, unless the string contains a quote or apostrophe (examples: ‘The Matrix’, “Something’s Gotta Give”). Note that you can also escape single or double quotes within strings that are quoted with the same using a backslash character.
+ Specify variables only when needed for use later in the Cypher statement.
+ Place named nodes and relationships (that use variables) before anonymous nodes and relationships in your MATCH clauses when possible.
+ Specify anonymous relationships with -->, --, or <--

Here is an example showing some best coding practices:

```javascript
MATCH (:Person {name: 'Diane Keaton'})-[movRel:ACTED_IN]->
(:Movie {title:"Something's Gotta Give"})
RETURN movRel.roles
```

We recommend that you follow the [Cypher Style Guide](https://github.com/opencypher/openCypher/blob/master/docs/style-guide.adoc) when writing your Cypher statements.

## Exercise 3: Filtering queries using relationships

In the query edit pane of Neo4j Browser, execute the browser command: `:play intro-neo4j-exercises` and follow the instructions for Exercise 3.

```javascript
// Exercise 3.1: Display the schema of the database (Solution)
CALL db.schema

// Exercise 3.2: Retrieve all people who wrote the movie Speed Racer (Solution)
MATCH (p:Person)-[:WROTE]->(:Movie {title: 'Speed Racer'}) RETURN p.name

// Exercise 3.3: Retrieve all movies that are connected to the person, Tom Hanks (Solution)
MATCH (m:Movie)<--(:Person {name: 'Tom Hanks'}) RETURN m.title

// Exercise 3.4: Retrieve information about the relationships Tom Hanks has with the set of movies retrieved earlier (Solution)
MATCH (m:Movie)-[rel]-(:Person {name: 'Tom Hanks'}) RETURN m.title, type(rel)

// Exercise 3.5: Retrieve information about the roles that Tom Hanks acted in (Solution)
MATCH (m:Movie)-[rel:ACTED_IN]-(:Person {name: 'Tom Hanks'}) RETURN m.title, rel.roles
```

## Check your understanding

### Question 1: Suppose you have a graph that contains nodes representing customers and other business entities for your application. The node label in the database for a customer is Customer. Each Customer node has a property named email that contains the customer’s email address. What Cypher query do you execute to return the email addresses for all customers in the graph?

[] MATCH (n) RETURN n.Customer.email

[ X ] MATCH (c:Customer) RETURN c.email

[] MATCH (Customer) RETURN email

[] MATCH (c) RETURN Customer.email

### Question 2: Suppose you have a graph that contains Customer and Product nodes. A Customer node can have a BOUGHT relationship with a Product node. Customer nodes can have other relationships with Product nodes. A Customer node has a property named customerName. A Product node has a property named productName. What Cypher query do you execute to return all of the products (by name) bought by customer ‘ABCCO’.

[ ] MATCH (c:Customer {customerName: 'ABCCO'}) RETURN c.BOUGHT.productName

[ ] MATCH (:Customer 'ABCCO')-[:BOUGHT]->(p:Product) RETURN p.productName

[ ] MATCH (p:Product)<-[:BOUGHT_BY]-(:Customer 'ABCCO') RETURN p.productName

[ X ] MATCH (:Customer {customerName: 'ABCCO'})-[:BOUGHT]->(p:Product) RETURN p.productName

### Question 3: When must you use a variable in a MATCH clause?

[ ] When you want to query the graph using a node label.

[ ] When you specify a property value to match the query.

[ X ] When you want to use the node or relationship to return a result.

[ ] When the query involves 2 types of nodes.

## Summary
