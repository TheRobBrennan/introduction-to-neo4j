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

### Cypher is ASCII art

### Nodes

### Labels

### Comments in Cypher

## Examining the data model

## Using MATCH to retrieve nodes

### Viewing nodes as table data

## Exercise 1: Retrieving nodes

## Properties

### Examining property keys

### Retrieving nodes filtered by a property value

### Returning property values

### Specifying aliases

## Exercise 2: Filtering queries using property values

## Relationships

### ASCII art

### Querying using relationships

### Examining relationships

### Using a relationship in a query

### Querying by multiple relationships

### Using anonymous nodes in a query

### Using an anonymous relationship for a query

### Retrieving the relationship types

### Retrieving properties for relationships

## Using patterns for queries

### Querying by any direction of the relationship

### Traversing relationships

### Using relationship direction to optimize a query

## Cypher style recommendations

## Exercise 3: Filtering queries using relationships

## Check your understanding

### Question 1: Suppose you have a graph that contains nodes representing customers and other business entities for your application. The node label in the database for a customer is Customer. Each Customer node has a property named email that contains the customer’s email address. What Cypher query do you execute to return the email addresses for all customers in the graph?

[ ] MATCH (n) RETURN n.Customer.email
[ ] MATCH (c:Customer) RETURN c.email
[ ] MATCH (Customer) RETURN email
[ ] MATCH (c) RETURN Customer.email

### Question 2: Suppose you have a graph that contains Customer and Product nodes. A Customer node can have a BOUGHT relationship with a Product node. Customer nodes can have other relationships with Product nodes. A Customer node has a property named customerName. A Product node has a property named productName. What Cypher query do you execute to return all of the products (by name) bought by customer ‘ABCCO’.

[ ] MATCH (c:Customer {customerName: 'ABCCO'}) RETURN c.BOUGHT.productName
[ ] MATCH (:Customer 'ABCCO')-[:BOUGHT]->(p:Product) RETURN p.productName
[ ] MATCH (p:Product)<-[:BOUGHT_BY]-(:Customer 'ABCCO') RETURN p.productName
[ ] MATCH (:Customer {customerName: 'ABCCO'})-[:BOUGHT]->(p:Product) RETURN p.productName

### Question 3: When must you use a variable in a MATCH clause?

[ ] When you want to query the graph using a node label.
[ ] When you specify a property value to match the query.
[ ] When you want to use the node or relationship to return a result.
[ ] When the query involves 2 types of nodes.

## Summary
