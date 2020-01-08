# Welcome

This folder has been created to track my work and ideas while studying [Creating Nodes and Relationships](https://neo4j.com/graphacademy/online-training/introduction-to-neo4j/part-6/)

For my own learning purposes, I will include sections of text from the original course to solidify my own understanding of the concepts presented.

## About this module

At the end of this module, you should be able to write Cypher statements to:

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

## Creating nodes

Recall that a node is an element of a graph representing a domain entity that has zero or more labels, properties, and relationships to or from other nodes in the graph.

When you create a node, you can add it to the graph without connecting it to another node.

Here is the simplified syntax for creating a node:

```javascript
CREATE (optionalVariable optionalLabels {optionalProperties})
```

If you plan on referencing the newly created node, you must provide a variable. Whether you provide labels or properties at node creation time is optional. In most cases, you will want to provide some label and property values for a node when created. This will enable you to later retrieve the node. Provided you have a reference to the node (for example, using a MATCH clause), you can always add, update, or remove labels and properties at a later time.

Here are some examples of creating a single node in Cypher:

```javascript
// This node can be retrieved using the title. A set of nodes with the label Movie can also be retrieved which will contain this node
CREATE (:Movie {title: 'Batman Begins'})

// Add a node with two labels to the graph of types Movie and Action with the title Batman Begins. This node can be retrieved using the title. A set of nodes with the labels Movie or Action can also be retrieved which will contain this node
CREATE (:Movie:Action {title: 'Batman Begins'})

// Add a node to the graph of types Movie and Action with the title Batman Begins. This node can be retrieved using the title. A set of nodes with the labels Movie or Action can also be retrieved which will contain this node. The variable m can be used for later processing after the CREATE clause:
CREATE (m:Movie:Action {title: 'Batman Begins'})

// Add a node to the graph of types_Movie_ and Action with the title Batman Begins. This node can be retrieved using the title. A set of nodes with the labels Movie or Action can also be retrieved which will contain this node. Here we return the title of the node
CREATE (m:Movie:Action {title: ' Batman Begins'})
RETURN m.title
```

### Creating multiple nodes

You can create multiple nodes by simply separating the nodes specified with commas, or by specifying multiple CREATE statements.

Here is an example, where we create some Person nodes that will represent some of the people associated with the movie Batman Begins:

```javascript
CREATE
(:Person {name: 'Michael Caine', born: 1933}),
(:Person {name: 'Liam Neeson', born: 1952}),
(:Person {name: 'Katie Holmes', born: 1978}),
(:Person {name: 'Benjamin Melniker', born: 1913})
```

The graph engine will create a node with the same properties of a node that already exists. You can prevent this from happening in one of two ways:

1. You can use MERGE rather than CREATE when creating the node.
2. You can add constraints to your graph.

You will learn about merging data later in this module. Constraints are configured globally for a graph and are covered later in this training.

### Adding labels to a node

You may not know ahead of time what label or labels you want for a node when it is created. You can add labels to a node using the SET clause.

Here is the simplified syntax for adding labels to a node:

```javascript
SET x:Label         // adding one label to node referenced by the variable x

SET x:Label1:Label2 // adding two labels to node referenced by the variable x
```

If you attempt to add a label to a node for which the label already exists, the SET processing is ignored.

Here is an example where we add the Action label to the node that has a label, Movie:

```javascript
MATCH (m:Movie)
WHERE m.title = 'Batman Begins'
SET m:Action
RETURN labels(m)
```

Notice here that we call the built-in function, labels() that returns the set of labels for the node.

### Removing labels from a node

Perhaps your data model has changed or the underlying data for a node has changed so that the label for a node is no longer useful or valid.

Here is the simplified syntax for removing labels from a node:

```javascript
REMOVE x:Label    // remove the label from the node referenced by the variable x
```

If you attempt to remove a label from a node for which the label does not exist, the SET processing is ignored.

Here is an example where we remove the Action label from the node that has a labels, Movie and Action:

```javascript
MATCH (m:Movie:Action)
WHERE m.title = 'Batman Begins'
REMOVE m:Action
RETURN labels(m)
```

### Adding properties to a node

After you have created a node and have a reference to the node, you can add properties to the node, again using the SET keyword.

Here are simplified syntax examples for adding properties to a node referenced by the variable x:

```javascript
SET x.propertyName = value

SET x.propertyName1 = value1, x.propertyName2 = value2

SET x = {propertyName1: value1, propertyName2: value2}

SET x += {propertyName1: value1, propertyName2: value2}
```

If the property does not exist, it is added to the node. If the property exists, its value is updated. If the value specified is null, the property is removed.

Note that the type of data for a property is not enforced. That is, you can assign a string value to a property that was once a numeric value and visa versa.

When specify the JSON-style object for assignment (using =) of the property values for the node, the object must include all of the properties and their values for the node as the existing properties for the node are overwritten. However, if you specify += when assigning to a property, the value at valueX is updated if the propertyNnameX exists for the node. If the propertyNameX does not exist for the node, then the property is added to the node.

Here is an example where we add the properties released and lengthInMinutes to the movie Batman Begins:

```javascript
MATCH (m:Movie)
WHERE m.title = 'Batman Begins'
SET m.released = 2005, m.lengthInMinutes = 140
RETURN m
```

Here is another example where we set the property values to the movie node using the JSON-style object containing the property keys and values. Note that all properties must be included in the object:

```javascript
MATCH (m:Movie)
WHERE m.title = 'Batman Begins'
SET  m = {title: 'Batman Begins',
          released: 2005,
          lengthInMinutes: 140,
          videoFormat: 'DVD',
          grossMillions: 206.5}
RETURN m
```

Note that when you add a property to a node for the first time in the graph, the property key is added to the graph. So for example, in the previous example, we added the videoFormat and grossMillions property keys to the graph as they have never been used before for a node in the graph. Once a property key is added to the graph, it is never removed. When you examine the property keys in the database (by executing CALL db.propertyKeys(), you will see all property keys created for the graph, regardless of whether they are currently used for nodes and relationships.

Here is an example where we use the JSON-style object to add the awards property to the node and update the grossMillions property:

```javascript
MATCH (m:Movie)
WHERE m.title = 'Batman Begins'
SET  m += { grossMillions: 300,
            awards: 66}
RETURN m
```

### Removing properties from a node

There are two ways that you can remove a property from a node. One way is to use the REMOVE keyword. The other way is to set the property’s value to null.

Here are simplified syntax examples for removing properties from a node referenced by the variable x:

```javascript
REMOVE x.propertyName

SET x.propertyName = null   // REMEMBER: Setting a property to null will remove it
```

Suppose we determined that no other Movie node in the graph has the properties, videoFormat and grossMillions. There is no restriction that nodes of the same type must have the same properties. However, we have decided that we want to remove these properties from this node. Here is example Cypher to remove this property from this Batman Begins node:

```javascript
MATCH (m:Movie)
WHERE m.title = 'Batman Begins'
SET m.grossMillions = null
REMOVE m.videoFormat
RETURN m
```

Assuming that we have previously created the node for the movie with the these properties, here is the result of running this Cypher statement where we remove each property a different way. One way we remove the property using the SET clause to set the property to null. And in another way, we use the REMOVE clause.

### Exercise 8: Creating Nodes

```javascript
// Exercise 8.1: Create a Movie node.
CREATE (:Movie {title: 'Forrest Gump'})

// Exercise 8.2: Retrieve the newly-created node.
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
RETURN m

// Exercise 8.3: Create a Person node.
CREATE (:Person {name: 'Robin Wright'})

// Exercise 8.4: Retrieve the newly-created node.
MATCH (p:Person)
WHERE p.name = 'Robin Wright'
RETURN p

// Exercise 8.5: Add a label to a node.
// Add the label OlderMovie to any Movie node that was released before 2010.
MATCH (m:Movie)
WHERE m.released < 2010
SET m:OlderMovie
RETURN DISTINCT labels(m)

// Exercise 8.6: Retrieve the node using the new label.
MATCH (m:OlderMovie)
RETURN m.title, m.released

// Exercise 8.7: Add the Female label to selected nodes.
// Add the label Female to all Person nodes that has a person whose name starts with Robin.
MATCH (p:Person)
WHERE p.name STARTS WITH 'Robin'
SET p:Female

// Exercise 8.8: Retrieve all Female nodes.
MATCH (p:Female)
RETURN p.name

// Exercise 8.9: Remove the Female label from the nodes that have this label.
MATCH (p:Female)
REMOVE p:Female

// Exercise 8.10: View the current schema of the graph.
CALL db.schema

// Exercise 8.11: Add properties to a movie.
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
SET m:OlderMovie,
    m.released = 1994,
    m.tagline = "Life is like a box of chocolates...you never know what you're gonna get.",
    m.lengthInMinutes = 142

// Exercise 8.12: Retrieve an OlderMovie node to confirm the label and properties.
MATCH (m:OlderMovie)
WHERE m.title = 'Forrest Gump'
RETURN m

// Exercise 8.13: Add properties to the person, Robin Wright.
MATCH (p:Person)
WHERE p.name = 'Robin Wright'
SET p.born = 1966, p.birthPlace = 'Dallas'

// Exercise 8.14: Retrieve an updated Person node.
MATCH (p:Person)
WHERE p.name = 'Robin Wright'
RETURN p

// Exercise 8.15: Remove a property from a Movie node.
// (!) - This example did not automatically load in the code block (!)
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
SET m.lengthInMinutes = null

// Exercise 8.16: Retrieve the node to confirm that the property has been removed.
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
RETURN m

// Exercise 8.17: Remove a property from a Person node.
MATCH (p:Person)
WHERE p.name = 'Robin Wright'
REMOVE p.birthPlace

// Exercise 8.18: Retrieve the node to confirm that the property has been removed.
MATCH (p:Person)
WHERE p.name = 'Robin Wright'
RETURN p
```

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
