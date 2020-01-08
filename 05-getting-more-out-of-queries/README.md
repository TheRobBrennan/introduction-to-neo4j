# Welcome

This folder has been created to track my work and ideas while studying [Getting More Out of Queries](https://neo4j.com/graphacademy/online-training/introduction-to-neo4j/part-5/)

For my own learning purposes, I will include sections of text from the original course to solidify my own understanding of the concepts presented.

## About this module

### Filtering queries using WHERE
#### Specifying ranges in WHERE clauses
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
