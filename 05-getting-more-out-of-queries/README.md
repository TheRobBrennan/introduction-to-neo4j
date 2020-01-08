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

#### Testing labels

#### Testing the existence of a property

#### Testing strings

#### Testing with regular expressions

#### Testing with patterns

#### Testing with list values

### Exercise 4: Filtering queries using the WHERE clause

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
