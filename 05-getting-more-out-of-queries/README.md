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

#### Specifying multiple MATCH patterns

#### Example 1: Using two MATCH patterns

#### Example 2: Using two MATCH patterns

#### Setting path variables

#### Specifying varying length paths

#### Finding the shortest path

#### Specifying optional pattern matching

#### Aggregation in Cypher

#### Collecting results

#### Counting results

#### Additional processing using WITH

### Exercise 5: Controlling query processing

### Controlling how results are returned

#### Eliminating duplication

#### Using WITH and DISTINCT to eliminate duplication

#### Ordering results

#### Limiting the number of results

#### Controlling the number of results using WITH

### Exercise 6: Controlling results returned

### Working with Cypher data

#### Lists

#### Unwinding lists

#### Dates

### Exercise 7: Working with Cypher data

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
