# Welcome

This folder has been created to track my work and ideas while studying [Creating Nodes and Relationships](https://neo4j.com/graphacademy/online-training/introduction-to-neo4j/part-6/)

For my own learning purposes, I will include sections of text from the original course to solidify my own understanding of the concepts presented.

## About this module

## Creating nodes

### Creating multiple nodes

### Adding labels to a node

### Removing labels from a node

### Adding properties to a node

### Removing properties from a node

### Exercise 8: Creating Nodes

## Creating relationships

### Adding properties to relationships

### Removing properties from a relationship

### Exercise 9: Creating Relationships

## Deleting nodes and relationships

### Deleting relationships

### Deleting nodes and relationships

### Exercise 10: Deleting Nodes and Relationships

## Merging data in the graph

### Using MERGE to create nodes

### Using MERGE to create relationships

### Specifying creation behavior when merging

### Using MERGE to create relationships

### Exercise 11: Merging Data in the graph

## Check your understanding

### Question 1

What Cypher clauses can you use to create a node?

Select the correct answers.

[ ] CREATE
[ ] CREATE NODE
[ ] MERGE
[ ] ADD

### Question 2

Suppose that you have retrieved a node, s with a property, color:

```javascript
MATCH (s:Shape {location: [20,30]})
???
RETURN s
```

What Cypher clause do you add here to delete the color property from this node?

Select the correct answers.

[ ] DELETE s.color
[ ] SET s.color=null
[ ] REMOVE s.color
[ ] SET s.color=?

### Question 3

Suppose you retrieve a node, n in the graph that is related to other nodes. What Cypher clause do you write to delete this node and its relationships in the graph?

Select the correct answer.

[ ] DELETE n
[ ] DELETE n WITH RELATIONSHIPS
[ ] REMOVE n
[ ] DETACH DELETE n

## Summary

You should now be able to write Cypher statements to:

+ Create a node
  + Add and remove node labels
  + Add and remove node properties
  + Update properties
+ Create a relationship
  + Add and remove properties for a relationship
+ Delete a node
+ Delete a relationship
+ Merge data in a graph
  + Creating nodes
  + Creating relationships
