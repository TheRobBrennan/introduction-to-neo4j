# Welcome

This folder has been created to track my work and ideas while studying [Getting More Out of Queries](https://neo4j.com/graphacademy/online-training/introduction-to-neo4j/part-5/)

For my own learning purposes, I will include sections of text from the original course to solidify my own understanding of the concepts presented.

## About this module

You have learned how to query nodes and relationships in a graph using simple patterns. You learned how to use node labels, relationship types, and properties to filter your queries. Cypher provides a rich set of MATCH clauses and keywords you can use to get more out of your queries.

At the end of this module, you should be able to write Cypher statements to:

+ Filter queries using the WHERE clause
+ Control query processing
+ Control what results are returned
+ Work with Cypher lists and dates

### Filtering queries using WHERE

You have learned how to specify values for properties of nodes and relationships to filter what data is returned from the MATCH and RETURN clauses. The format for filtering you have learned thus far only tests equality, where you must specify values for the properties to test with. What if you wanted more flexibility about how the query is filtered? For example, you want to retrieve all movies released after 2000, or retrieve all actors born after 1970 who acted in movies released before 1995. Most applications need more flexibility in how data is filtered.

The most common clause you use to filter queries is the WHERE clause that follows a MATCH clause. In the WHERE clause, you can place conditions that are evaluated at runtime to filter the query.

Previously, you learned to write simple query as follows:

```javascript
MATCH (p:Person)-[:ACTED_IN]->(m:Movie {released: 2008})
RETURN p, m
```

Here is one way you specify the same query using the WHERE clause:

```javascript
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released = 2008
RETURN p, m
```

In this example, you can only refer to named nodes or relationships in a WHERE clause so remember that you must specify a variable for any node or relationship you are testing in the WHERE clause. The benefit of using a WHERE clause is that you can specify potentially complex conditions for the query:

```javascript
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released = 2008 OR m.released = 2009
RETURN p, m
```

#### Specifying ranges in WHERE clauses

Not only can the equality = be tested, but you can test ranges, existence, strings, as well as specify logical operations during the query.

Here is an example of specifying a range for filtering the query:

```javascript
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released >= 2003 AND m.released <= 2004
RETURN p.name, m.title, m.released
```

You can also specify the same query as:

```javascript
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE 2003 <= m.released <= 2004
RETURN p.name, m.title, m.released
```

You can specify conditions in a WHERE clause that return a value of true or false (for example predicates). For testing numeric values, you use the standard numeric comparison operators. Each condition can be combined for runtime evaluation using the boolean operators AND, OR, XOR, and NOT. There are a number of numeric functions you can use in your conditions. See the Neo4j Cypher Manual’s section Mathematical Functions for more information.

A special condition in a query is when the retrieval returns an unknown value called null. You should read the Neo4j Cypher Manual’s section Working with null to understand how null values are used at runtime.

#### Testing labels

Thus far, you have used the node labels to filter queries in a MATCH clause. You can filter node labels in the WHERE clause also:

For example, these two Cypher queries:

```javascript
MATCH (p:Person)
RETURN p.name

MATCH (p:Person)-[:ACTED_IN]->(:Movie {title: 'The Matrix'})
RETURN p.name
```

can be rewritten using WHERE clauses as follows:

```javascript
MATCH (p)
WHERE p:Person
RETURN p.name

MATCH (p)-[:ACTED_IN]->(m)
WHERE p:Person AND m:Movie AND m.title='The Matrix'
RETURN p.name
```

Not all node labels need to be tested during a query, but if your graph has multiple labels for the same node, filtering it by the node label will provide better query performance.

#### Testing the existence of a property

Recall that a property is associated with a particular node or relationship. A property is not associated with a node with a particular label or relationship type. In one of our queries earlier, we saw that the movie “Something’s Gotta Give” is the only movie in the Movie database that does not have a tagline property. Suppose we only want to return the movies that the actor, Jack Nicholson acted in with the condition that they must all have a tagline.

Here is the query to retrieve the specified movies where we test the existence of the tagline property:

```javascript
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name='Jack Nicholson' AND exists(m.tagline)
RETURN m.title, m.tagline
```

#### Testing strings

Cypher has a set of string-related keywords that you can use in your WHERE clauses to test string property values. You can specify `STARTS WITH`, `ENDS WITH`, and `CONTAINS`.

For example, to find all actors in the Movie database whose first name is Michael, you would write:

```javascript
MATCH (p:Person)-[:ACTED_IN]->()
WHERE p.name STARTS WITH 'Michael'
RETURN p.name
```

Note that the comparison of strings is case-sensitive. There are a number of string-related functions you can use to further test strings. For example, if you want to test a value, regardless of its case, you could call the toLower() function to convert the string to lower case before it is compared.

```javascript
MATCH (p:Person)-[:ACTED_IN]->()
WHERE toLower(p.name) STARTS WITH 'michael'
RETURN p.name
```

NOTE	In this example where we are converting a property to lower case, if an index has been created for this property, it will not be used at runtime.

See the String functions section of the Neo4j Cypher Manual for more information. It is sometimes useful to use the built-in string functions to modify the data that is returned in the query in the RETURN clause.

#### Testing with regular expressions

If you prefer, you can test property values using regular expressions. You use the syntax =~ to specify the regular expression you are testing with. Here is an example where we test the name of the Person using a regular expression to retrieve all Person nodes with a name property that begins with ‘Tom’:

```javascript
MATCH (p:Person)
WHERE p.name =~'Tom.*'
RETURN p.name
```

NOTE If you specify a regular expression. The index will never be used. In addition, the property value must fully match the regular expression.

#### Testing with patterns

Sometimes during a query, you may want to perform additional filtering using the relationships between nodes being visited during the query. For example, during retrieval, you may want to exclude certain paths traversed. You can specify a NOT specifier on a pattern in a WHERE clause.

Here is an example where we want to return all Person nodes of people who wrote movies:

```javascript
MATCH (p:Person)-[:WROTE]->(m:Movie)
RETURN p.name, m.title
```

Next, we modify this query to exclude people who directed that movie:

```javascript
MATCH (p:Person)-[:WROTE]->(m:Movie)
WHERE NOT exists( (p)-[:DIRECTED]->(m) )
RETURN p.name, m.title
```

Here is another example where we want to find Gene Hackman and the movies that he acted in with another person who also directed the movie:

```javascript
MATCH (gene:Person)-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(other:Person)
WHERE gene.name= 'Gene Hackman'
AND exists( (other)-[:DIRECTED]->(m) )
RETURN  gene, other, m
```

#### Testing with list values

If you have a set of values you want to test with, you can place them in a list or you can test with an existing list in the graph.

You can define the list in the WHERE clause. During the query, the graph engine will compare each property with the values IN the list. You can place either numeric or string values in the list, but typically, elements of the list are of the same type of data. If you are testing with a property of a string type, then all the elements of the list should be strings.

In this example, we only want to retrieve Person nodes of people born in 1965 or 1970:

```javascript
MATCH (p:Person)
WHERE p.born IN [1965, 1970]
RETURN p.name as name, p.born as yearBorn
```

You can also compare a value to an existing list in the graph.

We know that the :ACTED_IN relationship has a property, roles that contains the list of roles an actor had in a particular movie they acted in. Here is the query we write to return the name of the actor who played Neo in the movie The Matrix:

```javascript
MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
WHERE  'Neo' IN r.roles AND m.title='The Matrix'
RETURN p.name
```

There are a number of syntax elements of Cypher that we have not covered in this training. For example, you can specify CASE logic in your conditional testing for your WHERE clauses. You can learn more about these syntax elements in the Neo4j Cypher Manual and the Cypher Refcard.

### Exercise 4: Filtering queries using the WHERE clause

In the query edit pane of Neo4j Browser, execute the browser command: `:play intro-neo4j-exercises` and follow the instructions for Exercise 4.

```javascript
// Exercise 4.1: Retrieve all movies that Tom Cruise acted in.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WHERE a.name = 'Tom Cruise'
RETURN m.title as Movie

// Exercise 4.2: Retrieve all people that were born in the 70’s.
MATCH (a:Person)
WHERE a.born >= 1970 AND a.born < 1980
RETURN a.name as Name, a.born as `Year Born`

// Exercise 4.3: Retrieve the actors who acted in the movie The Matrix who were born after 1960.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WHERE a.born > 1960 AND m.title = 'The Matrix'
RETURN a.name as Name, a.born as `Year Born`

// Exercise 4.4: Retrieve all movies by testing the node label and a property.
MATCH (m)
WHERE m:Movie AND m.released = 2000
RETURN m.title

// Exercise 4.5: Retrieve all people that wrote movies by testing the relationship between two nodes.
MATCH (a)-[rel]->(m)
WHERE a:Person AND type(rel) = 'WROTE' AND m:Movie
RETURN a.name as Name, m.title as Movie

// Exercise 4.6: Retrieve all people in the graph that do not have a property.
MATCH (a:Person)
WHERE NOT exists(a.born)
RETURN a.name as Name

// Exercise 4.7: Retrieve all people related to movies where the relationship has a property.
MATCH (a:Person)-[rel]->(m:Movie)
WHERE exists(rel.rating)
RETURN a.name as Name, m.title as Movie, rel.rating as Rating

// Exercise 4.8: Retrieve all actors whose name begins with James.
MATCH (a:Person)-[:ACTED_IN]->(:Movie)
WHERE a.name STARTS WITH 'James'
RETURN a.name

// Exercise 4.9: Retrieve all REVIEWED relationships from the graph with filtered results
MATCH (:Person)-[r:REVIEWED]->(m:Movie)
WHERE toLower(r.summary) CONTAINS 'fun'
RETURN  m.title as Movie, r.summary as Review, r.rating as Rating

// Exercise 4.10: Retrieve all people who have produced a movie, but have not directed a movie
MATCH (a:Person)-[:PRODUCED]->(m:Movie)
WHERE NOT ((a)-[:DIRECTED]->(:Movie))
RETURN a.name, m.title

// Exercise 4.11: Retrieve the movies and their actors where one of the actors also directed the movie
MATCH (a1:Person)-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(a2:Person)
WHERE exists( (a2)-[:DIRECTED]->(m) )
RETURN  a1.name as Actor, a2.name as `Actor/Director`, m.title as Movie

// Exercise 4.12: Retrieve all movies that were released in a set of years
MATCH (m:Movie)
WHERE m.released in [2000, 2004, 2008]
RETURN m.title, m.released

// Exercise 4.13: Retrieve the movies that have an actor’s role that is the name of the movie
MATCH (a:Person)-[r:ACTED_IN]->(m:Movie)
WHERE m.title in r.roles
RETURN  m.title as Movie, a.name as Actor
```

### Controlling query processing

Now that you have learned how to provide filters for your queries by testing properties, relationships, and patterns using the WHERE clause, you will learn some additional Cypher techniques for controlling what the graph engine does during the query.

#### Specifying multiple MATCH patterns

This MATCH clause includes a pattern specified by two paths separated by a comma:

```javascript
MATCH (a:Person)-[:ACTED_IN]->(m:Movie),
      (m:Movie)<-[:DIRECTED]-(d:Person)
WHERE m.released = 2000
RETURN a.name, m.title, d.name
```

If possible, you should write the same query as follows:

```javascript
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person)
WHERE m.released = 2000
RETURN a.name, m.title, d.name
```

There are, however, some queries where you will need to specify two or more patterns. Multiple patterns are used when a query is complex and cannot be satisfied with a single pattern. This is useful when you are looking for a specific node in the graph and want to connect it to a different node. You will learn about creating nodes and relationships later in this training.

#### Example 1: Using two MATCH patterns

Here are some examples of specifying two paths in a MATCH clause. In the first example, we want the actors that worked with Keanu Reeves to meet Hugo Weaving, who has worked with Keanu Reeves. Here we retrieve the actors who acted in the same movies as Keanu Reeves, but not when Hugo Weaving acted in the same movie. To do this, we specify two paths for the MATCH:

```javascript
MATCH (keanu:Person)-[:ACTED_IN]->(movie:Movie)<-[:ACTED_IN]-(n:Person),
     (hugo:Person)
WHERE keanu.name='Keanu Reeves' AND
      hugo.name='Hugo Weaving'
AND NOT (hugo)-[:ACTED_IN]->(movie)
RETURN n.name
```

#### Example 2: Using two MATCH patterns

Here is another example where two patterns are necessary. Suppose we want to retrieve the movies that Meg Ryan acted in and their respective directors, as well as the other actors that acted in these movies. Here is the query to do this:

```javascript
MATCH (meg:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person),
      (other:Person)-[:ACTED_IN]->(m)
WHERE meg.name = 'Meg Ryan'
RETURN m.title as movie, d.name AS director , other.name AS `co-actors`
```

#### Setting path variables

You have previously seen how you can assign a path used in a MATCH clause to a variable. This is useful if you want to reuse the path later in the same query or if you want to return the path. So the previous Cypher statement could return the path as follows:

```javascript
MATCH megPath = (meg:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person),
      (other:Person)-[:ACTED_IN]->(m)
WHERE meg.name = 'Meg Ryan'
RETURN megPath
```

#### Specifying varying length paths

Any graph that represents social networking, trees, or hierarchies will most likely have multiple paths of varying lengths. Think of the connected relationship in LinkedIn and how connections are made by people connected to more people. The Movie database for this training does not have much depth of relationships, but it does have the :FOLLOWS relationship that you learned about earlier:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/FollowsRelationships.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/FollowsRelationships.png)

You write a MATCH clause where you want to find all of the followers of the followers of a Person by specifying a numeric value for the number of hops in the path. Here is an example where we want to retrieve all Person nodes that are exactly two hops away:

```javascript
MATCH (follower:Person)-[:FOLLOWS*2]->(p:Person)
WHERE follower.name = 'Paul Blythe'
RETURN p
```

If we had specified [:FOLLOWS*] rather than [:FOLLOWS*2], the query would return all Person nodes that are in the :FOLLOWS path from Paul Blythe.

Here are simplified syntax examples for how varying length patterns are specified in Cypher:

```javascript
// Retrieve all paths of any length with the relationship, :RELTYPE from nodeA to nodeB and beyond
(nodeA)-[:RELTYPE*]->(nodeB)

// Retrieve all paths of any length with the relationship, :RELTYPE from nodeA to nodeB or from nodeB to nodeA and beyond. This is usually a very expensive query so you should place limits on how many nodes are retrieved
(nodeA)-[:RELTYPE*]-(nodeB)

// Retrieve the paths of length 3 with the relationship, :RELTYPE from nodeA to nodeB
(node1)-[:RELTYPE*3]->(node2)

// Retrieve the paths of lengths 1, 2, or 3 with the relationship, :RELTYPE from nodeA to nodeB, nodeB to nodeC, as well as, nodeC to _nodeD) (up to three hops)
(node1)-[:RELTYPE*1..3]->(node2)
```

You can learn more about varying paths in the Patterns section of the Neo4j Cypher Manual.

#### Finding the shortest path

A built-in function that you may find useful in a graph that has many ways of traversing the graph to get to the same node is the shortestPath() function. Using the shortest path between two nodes improves the performance of the query.

In this example, we want to discover a shortest path between the movies The Matrix and A Few Good Men. In our MATCH clause, we set the variable p to the result of calling shortestPath(), and then return p. In the call to shortestPath(), notice that we specify * for the relationship. This means any relationship; for the traversal.

```javascript
MATCH p = shortestPath((m1:Movie)-[*]-(m2:Movie))
WHERE m1.title = 'A Few Good Men' AND
      m2.title = 'The Matrix'
RETURN  p
```

Notice that the graph engine has traversed many types of relationships to get to the end node.

When you use the shortestPath() function, the query editor will show a warning that this type of query could potentially run for a long time. You should heed the warning, especially for large graphs. Read the Graph Algorithms documentation about the shortest path algorithm.

When you use ShortestPath(), you can specify a upper limits for the shortest path. In addition, you should aim to provide the patterns for the from an to nodes that execute efficiently. For example, use labels and indexes.

#### Specifying optional pattern matching

OPTIONAL MATCH matches patterns with your graph, just like MATCH does. The difference is that if no matches are found, OPTIONAL MATCH will use NULLs for missing parts of the pattern. OPTIONAL MATCH could be considered the Cypher equivalent of the outer join in SQL.

Here is an example where we query the graph for all people whose name starts with James. The OPTIONAL MATCH is specified to include people who have reviewed movies:

```javascript
MATCH (p:Person)
WHERE p.name STARTS WITH 'James'
OPTIONAL MATCH (p)-[r:REVIEWED]->(m:Movie)
RETURN p.name, type(r), m.title
```

Notice that for all rows that do not have the :REVIEWED relationship, a null value is returned for the movie part of the query, as well as the relationship.

#### Aggregation in Cypher

Aggregation in Cypher is different from aggregation in SQL. In Cypher, you need not specify a grouping key. As soon as an aggregation function is used, all non-aggregated result columns become grouping keys. The grouping is implicitly done, based upon the fields in the RETURN clause.

For example, in this Cypher statement, all rows returned with the same values for a.name and d.name are counted and only returned once:

```javascript
// implicitly groups by a.name and d.name
MATCH (a)-[:ACTED_IN]->(m)<-[:DIRECTED]-(d)
RETURN a.name, d.name, count(*)
```

#### Collecting results

Cypher has a built-in function, collect() that enables you to aggregate a value into a list. Here is an example where we collect the list of movies that Tom Cruise acted in:

```javascript
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name ='Tom Cruise'
RETURN collect(m.title) AS `movies for Tom Cruise`
```

In Cypher, there is no “GROUP BY” clause as there is in SQL. The graph engine uses non-aggregated columns as an automatic grouping key.

#### Counting results

The Cypher count() function is very useful when you want to count the number of occurrences of a particular query result. If you specify count(n), the graph engine calculates the number of occurrences of n. If you specify count(*), the graph engine calculates the number of rows retrieved, including those with null values. When you use count(), the graph engine does an implicit group by based upon the aggregation.

Here is an example where we count the paths retrieved where an actor and director collaborated in a movie and the count() function is used to count the number of paths found for each actor/director collaboration:

```javascript
MATCH (actor:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(director:Person)
RETURN actor.name, director.name, count(m) AS collaborations, collect(m.title) AS movies
```

There are more aggregating functions such as min() or max() that you can also use in your queries. These are described in the Aggregating Functions section of the Neo4j Cypher Manual.

#### Additional processing using WITH

During the execution of a MATCH clause, you can specify that you want some intermediate calculations or values that will be used for further processing of the query, or for limiting the number of results before further processing is done. You use the WITH clause to perform intermediate processing or data flow operations.

Here is an example where we start the query processing by retrieving all actors and their movies. During the query processing, want to only return actors that have 2 or 3 movies. All other actors and the aggregated results are filtered out. This type of query is a replacement for SQL’s “HAVING” clause. The WITH clause does the counting and collecting, but is then used in the subsequent WHERE clause to limit how many paths are visited:

```javascript
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH  a, count(a) AS numMovies, collect(m.title) as movies
WHERE numMovies > 1 AND numMovies < 4
RETURN a.name, numMovies, movies
```

When you use the WITH clause, you specify the variables from the previous part of the query you want to pass on to the next part of the query. In this example, the variable a is specified to be passed on in the query, but m is not. Since m is not specified to be passed on, m will not be available later in the query. Notice that for the RETURN clause, a, numMovies, and movies are available for use.

You have to name all expressions with an alias in a WITH that are not simple variables.

Here is another example where we want to find all actors who have acted in at least five movies, and find (optionally) the movies they directed and return the person and those movies:

```javascript
MATCH (p:Person)
WITH p, size((p)-[:ACTED_IN]->(:Movie)) AS movies
WHERE movies >= 5
OPTIONAL MATCH (p)-[:DIRECTED]->(m:Movie)
RETURN p.name, m.title
```

In this example, we first retrieve all people, but then specify a pattern in the WITH clause where we calculate the number of :ACTED_IN relationships retrieved using the size() function. If this value is greater than five, we then also retrieve the :DIRECTED paths to return the name of the person and the title of the movie they directed. In the result, we see that these actors acted in more than five movies, but Tom Hanks is the only actor who directed a movie and thus the only person to have a value for the movie.

### Exercise 5: Controlling query processing

In the query edit pane of Neo4j Browser, execute the browser command: `:play intro-neo4j-exercises` and follow the instructions for Exercise 5.

```javascript
// Exercise 5.1: Retrieve data using multiple MATCH patterns.
// Write a Cypher query that retrieves all movies that Gene Hackman has acted in, along with the directors of the movies. In addition, retrieve the actors that acted in the same movies as Gene Hackman. Return the name of the movie, the name of the director, and the names of actors that worked with Gene Hackman.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person),
      (a2:Person)-[:ACTED_IN]->(m)
WHERE a.name = 'Gene Hackman'
RETURN m.title as movie, d.name AS director , a2.name AS `co-actors`

// Exercise 5.2: Retrieve particular nodes that have a relationship.
// Retrieve all nodes that the person named James Thompson directly has the FOLLOWS relationship in either direction.
MATCH (p1:Person)-[:FOLLOWS]-(p2:Person)
WHERE p1.name = 'James Thompson'
RETURN p1, p2

// Exercise 5.3: Modify the query to retrieve nodes that are exactly three hops away.
// Modify the query to retrieve nodes that are exactly three hops away.
MATCH (p1:Person)-[:FOLLOWS*3]-(p2:Person)
WHERE p1.name = 'James Thompson'
RETURN p1, p2

// Exercise 5.4: Modify the query to retrieve nodes that are one and two hops away.
MATCH (p1:Person)-[:FOLLOWS*1..2]-(p2:Person)
WHERE p1.name = 'James Thompson'
RETURN p1, p2

// Exercise 5.5: Modify the query to retrieve particular nodes that are connected no matter how many hops are required.
// Modify the query to retrieve all nodes that are connected to James Thompson by a Follows relationship no matter how many hops are required.
MATCH (p1:Person)-[:FOLLOWS*]-(p2:Person)
WHERE p1.name = 'James Thompson'
RETURN p1, p2

// Exercise 5.6: Specify optional data to be retrieved during the query.
// Write a Cypher query to retrieve all people in the graph whose name begins with Tom and optionally retrieve all people named Tom who directed a movie.
MATCH (p:Person)
WHERE p.name STARTS WITH 'Tom'
OPTIONAL MATCH (p)-[:DIRECTED]->(m:Movie)
RETURN p.name, m.title

// Exercise 5.7: Retrieve nodes by collecting a list.
// Retrieve actors and the movies they have acted in, returning each actor’s name and the list of movies they acted in.
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
RETURN p.name as actor, collect(m.title) AS `movie list`

// Exercise 5.8: Retrieve all movies that Tom Cruise has acted in and the co-actors that acted in the same movie by collecting a list
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(p2:Person)
WHERE p.name ='Tom Cruise'
RETURN m.title as movie, collect(p2.name) AS `co-actors`

// Exercise 5.9: Retrieve nodes as lists and return data associated with the corresponding lists.
// Retrieve all people who reviewed a movie, returning the list of reviewers and how many reviewers reviewed the movie.
MATCH (p:Person)-[:REVIEWED]->(m:Movie)
RETURN m.title as movie, count(p) as numReviews, collect(p.name) as reviewers

// Exercise 5.10: Retrieve nodes and their relationships as lists.
// Retrieve all directors, their movies, and people who acted in the movies, returning the name of the director, the number of actors the director has worked with, and the list of actors.
MATCH (d:Person)-[:DIRECTED]->(m:Movie)<-[:ACTED_IN]-(a:Person)
RETURN d.name AS director, count(a) AS `number actors` , collect(a.name) AS `actors worked with`

// Exercise 5.11: Retrieve the actors who have acted in exactly five movies.
// Retrieve the actors who have acted in exactly five movies, returning the name of the actor, and the list of movies for that actor.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH  a, count(a) AS numMovies, collect(m.title) AS movies
WHERE numMovies = 5
RETURN a.name, movies

// Exercise 5.12: Retrieve the movies that have at least 2 directors with other optional data.
// Retrieve the movies that have at least 2 directors, and optionally the names of people who reviewed the movies.
MATCH (m:Movie)
WITH m, size((:Person)-[:DIRECTED]->(m)) AS directors
WHERE directors >= 2
OPTIONAL MATCH (p:Person)-[:REVIEWED]->(m)
RETURN  m.title, p.name
```

### Controlling how results are returned

#### Eliminating duplication

You have seen a number of query results where there is duplication in the results returned. In most cases, you want to eliminate duplicated results. You do so by using the DISTINCT keyword.

Here is a simple example where duplicate data is returned. Tom Hanks both acted in and directed the movie, That Thing You Do, so the movie is returned twice in the result stream:

```javascript
MATCH (p:Person)-[:DIRECTED | :ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks'
RETURN m.released, collect(m.title) AS movies
```

We can eliminate the duplication by specifying the DISTINCT keyword as follows:

```javascript
MATCH (p:Person)-[:DIRECTED | :ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks'
RETURN m.released, collect(DISTINCT m.title) AS movies
```

#### Using WITH and DISTINCT to eliminate duplication

Another way that you can avoid duplication is to with WITH and DISTINCT together as follows:

```javascript
MATCH (p:Person)-[:DIRECTED | :ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks'
WITH DISTINCT m
RETURN m.released, m.title
```

#### Ordering results

If you want the results to be sorted, you specify the expression to use for the sort using the ORDER BY keyword and whether you want the order to be descending using the DESC keyword. Ascending order is the default. Note that you can provide multiple sort expressions and the result will be sorted in that order. Just as you can use DISTINCT with WITH to eliminate duplication, you can use ORDER BY with WITH to control the sorting of results.

In this example, we specify that the release date of the movies for Tom Hanks will be returned in descending order:

```javascript
MATCH (p:Person)-[:DIRECTED | :ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks'
RETURN m.released, collect(DISTINCT m.title) AS movies ORDER BY m.released DESC
```

#### Limiting the number of results

Although you can filter queries to reduce the number of results returned, you may also want to limit the number of results. This is useful if you have very large result sets and you only need to see the beginning or end of a set of ordered results. You can use the LIMIT keyword to specify the number of results returned. Furthermore, you can use the LIMIT keyword with the WITH clause to limit results.

Suppose you want to see the titles of the ten most recently released movies. You could do so as follows where you limit the number of results using the LIMIT keyword as follows:

```javascript
MATCH (m:Movie)
RETURN m.title as title, m.released as year ORDER BY m.released DESC LIMIT 10
```

#### Controlling the number of results using WITH

Previously, you saw how you can use the WITH clause to perform some intermediate processing during a query. You can use the WITH clause to limit the number of results.

In this example, we count the number of movies during the query and we return the results once we have reached 5 movies:

```javascript
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH a, count(*) AS numMovies, collect(m.title) as movies
WHERE numMovies = 5
RETURN a.name, numMovies, movies
```

### Exercise 6: Controlling results returned

In the query edit pane of Neo4j Browser, execute the browser command: `:play intro-neo4j-exercises` and follow the instructions for Exercise 6.

```javascript
// Exercise 6.1: Execute a query that returns duplicate records.
// You want to know what actors acted in movies in the decade starting with the year 1990. First write a query to retrieve all actors that acted in movies during the 1990s, where you return the released date, the movie title, and the collected actor names for the movie. For now do not worry about duplication.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released >= 1990 AND m.released < 2000
RETURN DISTINCT m.released, m.title, collect(a.name)

// Exercise 6.2: Modify the query to eliminate duplication.
// The results returned from the previous query include multiple rows for a movie released value. Next, modify the query so that the released date records returned are not duplicated. To implement this, you must add the collection of the movie titles to the results returned.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released >= 1990 AND m.released < 2000
RETURN  m.released, collect(m.title), collect(a.name)

// Exercise 6.3: Modify the query to eliminate more duplication.
// The results returned from the previous query returns the collection of movie titles with duplicates. That is because there are multiple actors per released year. Next, modify the query so that there is no duplication of the movies listed for a year.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released >= 1990 AND m.released < 2000
RETURN  m.released, collect(DISTINCT m.title), collect(a.name)

// Exercise 6.4: Sort results returned.
// Modify the query that you just wrote to order the results returned so that the more recent years are displayed first.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released >= 1990 AND m.released < 2000
RETURN  m.released, collect(DISTINCT m.title), collect(a.name)
ORDER BY m.released DESC

// Exercise 6.5: Retrieve the top 5 ratings and their associated movies.
// Retrieve the top 5 ratings and their associated movies, returning the movie title and the rating.
MATCH (:Person)-[r:REVIEWED]->(m:Movie)
RETURN  m.title AS movie, r.rating AS rating
ORDER BY r.rating DESC LIMIT 5

// Exercise 6.6: Retrieve all actors that have not appeared in more than 3 movies.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH  a,  count(a) AS numMovies, collect(m.title) AS movies
WHERE numMovies <= 3
RETURN a.name, movies
```

### Working with Cypher data

Thus far, you have specified both string and numeric types in your Cypher queries. You have also learned that nodes and relationships can have properties, whose values are structured like JSON objects. You have also learned that the collect() function can create lists of values or objects where a list is comma-separated and you can use the IN keyword to search for a value in a list. Next, you will learn more about working with lists and dates in Cypher.

#### Lists

There are many built-in Cypher functions that you can use to build or access elements in lists. A Cypher map is list of key/value pairs where each element of the list is of the format key: value. For example, a map of months and the number of days per month could be:

`[Jan: 31, Feb: 28, Mar: 31, Apr: 30 , May: 31, Jun: 30 , Jul: 31, Aug: 31, Sep: 30, Oct: 31, Nov: 30, Dec: 31]`

You can collect values for a list during a query and when you return results, you can sort by the size of the list using the size() function as follows:

```javascript
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH  m, count(m) AS numCast, collect(a.name) as cast
RETURN m.title, cast, numCast ORDER BY size(cast)
```

You can read more about working with lists in the List Functions section of the Neo4j Cypher Manual.

#### Unwinding lists

There may be some situations where you want to perform the opposite of collecting results, but rather separate the lists into separate rows. This functionality is done using the UNWIND clause.

Here is an example where we create a list with three elements, unwind the list and then return the values. Since there are three elements, three rows are returned with the values:

```javascript
WITH [1, 2, 3] AS list
UNWIND list AS row
RETURN list, row
```

Notice that there is no MATCH clause. You need not query the database to execute Cypher statements, but you do need the RETURN clause here to return the calculated values from the Cypher query.

The UNWIND clause is frequently used when importing data into a graph.

#### Dates

Cypher has a built-in date() function, as well as other temporal values and functions that you can use to calculate temporal values. You use a combination of numeric, temporal, spatial, list and string functions to calculate values that are useful to your application. For example, suppose you wanted to calculate the age of a Person node, given a year they were born (the born property must exist and have a value).

Here is example Cypher to retrieve all actors from the graph, and if they have a value for born, calculate the age value:

```javascript
MATCH (actor:Person)-[:ACTED_IN]->(:Movie)
WHERE exists(actor.born)
// calculate the age
with DISTINCT actor, date().year  - actor.born as age
RETURN actor.name, age as `age today`
      ORDER BY actor.born DESC
```

Consult the Neo4j Cypher Manual for more information about the built-in functions available for working with data of all types:

+ Predicate
+ Scalar
+ List
+ Mathematical
+ String
+ Temporal
+ Spatial

### Exercise 7: Working with Cypher data

In the query edit pane of Neo4j Browser, execute the browser command: `:play intro-neo4j-exercises` and follow the instructions for Exercise 7.

```javascript
// Exercise 7.1: Collect and use lists.
/*
Write a Cypher query that retrieves all actors that acted in movies, and also retrieves the producers for those movies. During the query, collect the names of the actors and the names of the producers. Return the movie titles, along with the list of actors for each movie, and the list of producers for each movie making sure there is no duplication of data. Order the results returned based upon the size of the list of actors.

Hints: Use two patterns for the query, one for actors, and one for producers. Use a WITH clause to collect the values for the cast and producers. You can use the size() function to calculate the size of a list for ordering purposes.
*/
MATCH (a:Person)-[:ACTED_IN]->(m:Movie),
      (m)<-[:PRODUCED]-(p:Person)
WITH  m, collect(DISTINCT a.name) AS cast, collect(DISTINCT p.name) AS producers
RETURN DISTINCT m.title, cast, producers
ORDER BY size(cast)

// Exercise 7.2: Collect a list.
/*
Write a Cypher query that retrieves all actors that acted in movies, and collects the list of movies for any actor that acted in more than five movies. Return the name of the actor and the list.

Hint: Use a WITH clause and test the size of the collected list using the size() function.
*/
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WITH p, collect(m) AS movies
WHERE size(movies)  > 5
RETURN p.name, movies

// Exercise 7.3: Unwind a list.
/*
Modify the query you just wrote so that before the query processing ends, you unwind the list of movies and then return the name of the actor and the title of the associated movie

Hint: Use two WITH clauses and test the size of the collected list using the size() function.
*/
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WITH p, collect(m) AS movies
WHERE size(movies)  > 5
WITH p, movies UNWIND movies AS movie
RETURN p.name, movie.title

// Exercise 7.4: Perform a calculation with the date type.
// Write a query that retrieves all movies that Tom Hanks acted in, returning the title of the movie, the year the movie was released, the number of years ago that the movie was released, and the age of Tom when the movie was released.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WHERE a.name = 'Tom Hanks'
RETURN  m.title, m.released, date().year  - m.released as yearsAgoReleased, m.released  - a.born AS `age of Tom`
ORDER BY yearsAgoReleased
```

### Check your understanding

#### Question 1

Suppose you want to add a `WHERE` clause at the end of this statement to filter the results retrieved.

```javascript
MATCH (p:Person)-[rel]->(m:Movie)<-[:PRODUCED]-(:Person)
```

What variables, can you test in the `WHERE` clause:

Select the correct answers.

[ ] p
[ ] rel
[ ] m
[ ] PRODUCED

#### Question 2

Suppose you want to retrieve all movies that have a released property value that is 2000, 2002, 2004, 2006, or 2008. Here is an incomplete Cypher example to return the title property values of all movies released in these years:

```javascript
MATCH (m:Movie)
WHERE m.released XX [2000, 2002, 2004, 2006, 2008]
RETURN m.title
```

What keyword do you specify for XX?

Select the correct answer.

[ ] CONTAINS
[ ] IN
[ ] IS
[ ] EQUALS

#### Question 3

Given this Cypher query:

```javascript
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH  m, count(m) AS numMovies, collect(m.title) as movies
WHERE numMovies > 1 AND numMovies < 4
RETURN //??
```

What variables or aliases can be used to return values?

Select the correct answers.

[ ] a
[ ] m
[ ] numMovies
[ ] movies

### Summary

You should now be able to write Cypher statements to:

+ Filter queries using the WHERE clause
+ Control query processing
+ Control what results are returned
+ Work with Cypher lists and dates
