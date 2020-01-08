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

As you have learned in the previous exercises where you query the graph, you often query using connections between nodes. The connections capture the semantic relationships and context of the nodes in the graph.

Here is the simplified syntax for creating a relationship between two nodes referenced by the variables x and y:

```javascript
CREATE (x)-[:REL_TYPE]->(y)

CREATE (x)<-[:REL_TYPE]-(y)
```

When you create the relationship, it must have direction. You can query nodes for a relationship in either direction, but you must create the relationship with a direction. An exception to this is when you create a node using MERGE that you will learn about later in this module.

In most cases, unless you are connecting nodes at creation time, you will retrieve the two nodes, each with their own variables, for example, by specifying a WHERE clause to find them, and then use the variables to connect them.

Here is an example. We want to connect the actor, Michael Caine with the movie, Batman Begins. We first retrieve the nodes of interest, then we create the relationship:

```javascript
MATCH (a:Person), (m:Movie)
WHERE a.name = 'Michael Caine' AND m.title = 'Batman Begins'
CREATE (a)-[:ACTED_IN]->(m)
RETURN a, m
```

Before you run these Cypher statements, you may see a warning in Neo4j Browser that you are creating a query that is a cartesian product that could potentially be a performance issue. You will see this warning if you have no unique constraint on the lookup keys. You will learn about uniqueness constraints later in the next module. If you are familiar with the data in the graph and can be sure that the MATCH clauses will not retrieve large amounts of data, you can continue. In our case, we are simply looking up a particular Person node and a particular Movie node so we can create the relationship.

You can create multiple relationships at once by simply providing the pattern for the creation that includes the relationship types, their directions, and the nodes that you want to connect.

Here is an example where we have already created Person nodes for an actor, Liam Neeson, and a producer, Benjamin Melniker. We create two relationships in this example, one for ACTED_IN and one for PRODUCED:

```javascript
MATCH (a:Person), (m:Movie), (p:Person)
WHERE a.name = 'Liam Neeson' AND
      m.title = 'Batman Begins' AND
      p.name = 'Benjamin Melniker'
CREATE (a)-[:ACTED_IN]->(m)<-[:PRODUCED]-(p)
RETURN a, m, p
```

Here is the result of running this Cypher statement:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/CreateTwoRelationships.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/CreateTwoRelationships.png)

When you create relationships based upon a MATCH clause, you must be certain that only a single node is returned for the MATCH, otherwise multiple relationships will be created.

### Adding properties to relationships

You can add properties to a relationship, just as you add properties to a node. You use the SET clause to do so.

Here is the simplified syntax for adding properties to a relationship referenced by the variable r:

```javascript
SET r.propertyName = value

SET r.propertyName1 = value1    , r.propertyName2 = value2

SET r = {propertyName1: value1, propertyName2: value2}

SET r += {propertyName1: value1, propertyName2: value2}
```

If the property does not exist, it is added to the relationship. If the property exists, its value is updated for the relationship. When specify the JSON-style object for assignment to the relationship using =, the object must include all of the properties for the relationship, just as you need to do for nodes. If you use +=, you can add or update properties, just as you do for nodes.

Here is an example where we will add the roles property to the ACTED_IN relationship from Christian Bale to Batman Begins right after we have created the relationship:

```javascript
MATCH (a:Person), (m:Movie)
WHERE a.name = 'Christian Bale' AND m.title = 'Batman Begins'
CREATE (a)-[rel:ACTED_IN]->(m)
SET rel.roles = ['Bruce Wayne','Batman']
RETURN a, m
```

The roles property is a list so we add it as such. If the relationship had multiple properties, we could have added them as a comma separated list or as an object, like can do for node properties.

You can also add properties to a relationship when the relationship is created. Here is another way to create and add the properties for the relationship:

```javascript
MATCH (a:Person), (m:Movie)
WHERE a.name = 'Christian Bale' AND m.title = 'Batman Begins'
CREATE (a)-[:ACTED_IN {roles: ['Bruce Wayne', 'Batman']}]->(m)
RETURN a, m
```

By default, the graph engine will create a relationship between two nodes, even if one already exists. You can test to see if the relationship exists before you create it as follows:

```javascript
MATCH (a:Person),(m:Movie)
WHERE a.name = 'Christian Bale' AND
      m.title = 'Batman Begins' AND
      NOT exists((a)-[:ACTED_IN]->(m))
CREATE (a)-[rel:ACTED_IN]->(m)
SET rel.roles = ['Bruce Wayne','Batman']
RETURN a, rel, m
```

You can prevent duplication of relationships by merging data using the MERGE clause, rather than the CREATE clause. You will learn about merging data later in this module.

### Removing properties from a relationship

There are two ways that you can remove a property from a node. One way is to use the REMOVE keyword. The other way is to set the property’s value to null, just as you do for properties of nodes.

Suppose we have added the ACTED_IN relationship between Christian Bale and the movie, Batman Returns where the roles property is added to the relationship. Here is an example to remove the roles property, yet keep the ACTED_IN relationship:

```javascript
MATCH (a:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE a.name = 'Christian Bale' AND m.title = 'Batman Begins'
REMOVE rel.roles
RETURN a, rel, m
```

An alternative to `REMOVE rel.roles` would be `SET rel.roles = null`

### Exercise 9: Creating Relationships

In the query edit pane of Neo4j Browser, execute the browser command: `:play intro-neo4j-exercises` and follow the instructions for Exercise 9.

```javascript
// Exercise 9.1: Create ACTED_IN relationships.
// In the last exercise, you created the node for the movie, Forrest Gump and the person, Robin Wright. Create the ACTED_IN relationship between the actors, Robin Wright, Tom Hanks, and Gary Sinise and the movie, Forrest Gump.
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
MATCH (p:Person)
WHERE p.name = 'Tom Hanks' OR p.name = 'Robin Wright' OR p.name = 'Gary Sinise'
CREATE (p)-[:ACTED_IN]->(m)

// Exercise 9.2: Create DIRECTED relationships.
// Create the DIRECTED relationship between Robert Zemeckis and the movie, Forrest Gump.
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
MATCH (p:Person)
WHERE p.name = 'Robert Zemeckis'
CREATE (p)-[:DIRECTED]->(m)

// Exercise 9.3: Create a HELPED relationship.
// Create a new relationship, HELPED from Tom Hanks to Gary Sinise.
MATCH (p1:Person)
WHERE p1.name = 'Tom Hanks'
MATCH (p2:Person)
WHERE p2.name = 'Gary Sinise'
CREATE (p1)-[:HELPED]->(p2)

// Exercise 9.4: Query nodes and new relationships.
// Write a Cypher query to return all nodes connected to the movie, Forrest Gump, along with their relationships.
MATCH (p:Person)-[rel]-(m:Movie)
WHERE m.title = 'Forrest Gump'
RETURN p, rel, m

// Exercise 9.5: Add properties to relationships.
// Next, you will add some properties to the relationships that you just created.
// Add the roles property to the three ACTED_IN relationships that you just created to the movie, Forrest Gump using this information: Tom Hanks played the role, Forrest Gump. Robin Wright played the role, Jenny Curran. Gary Sinise played the role, Lieutenant Dan Taylor.
// Hint: You can set each relationship using separate MATCH clauses. You can also use a CASE clause to set the values. Look up in the documentation for how to use the CASE clause.
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE m.title = 'Forrest Gump'
SET rel.roles =
CASE p.name
  WHEN 'Tom Hanks' THEN ['Forrest Gump']
  WHEN 'Robin Wright' THEN ['Jenny Curran']
  WHEN 'Gary Sinise' THEN ['Lieutenant Dan Taylor']
END

// Exercise 9.6: Add a property to the HELPED relationship.
// Add a new property, research to the HELPED relationship between Tom Hanks and Gary Sinise and set this property’s value to war history.
MATCH (p1:Person)-[rel:HELPED]->(p2:Person)
WHERE p1.name = 'Tom Hanks' AND p2.name = 'Gary Sinise'
SET rel.research = 'war history'

// Exercise 9.7: View the current list of property keys in the graph.
call db.propertyKeys

// Exercise 9.8: View the current schema of the graph.
call db.schema

// Exercise 9.9: Retrieve the names and roles for actors.
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE m.title = 'Forrest Gump'
RETURN p.name, rel.roles

// Exercise 9.10: Retrieve information about any specific relationships.
MATCH (p1:Person)-[rel:HELPED]-(p2:Person)
RETURN p1.name, rel, p2.name

// Exercise 9.11: Modify a property of a relationship.
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE m.title = 'Forrest Gump' AND p.name = 'Gary Sinise'
SET rel.roles =['Lt. Dan Taylor']

// Exercise 9.12: Remove a property from a relationship.
// Remove the research property from the HELPED relationship from Tom Hanks to Gary Sinise.
MATCH (p1:Person)-[rel:HELPED]->(p2:Person)
WHERE p1.name = 'Tom Hanks' AND p2.name = 'Gary Sinise'
REMOVE rel.research

// Exercise 9.13: Confirm that your modifications were made to the graph.
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE m.title = 'Forrest Gump'
return p, rel, m
```

## Deleting nodes and relationships

If a node has no relationships to any other nodes, you can simply delete it from the graph using the DELETE clause. Relationships are also deleted using the DELETE clause.

If you attempt to delete a node in the graph that has relationships in or out of the node, the graph engine will return an error because deleting such a node will leave orphaned relationships in the graph.

### Deleting relationships

Here are the existing nodes and relationships for the Batman Begins movie:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/BatmanBeginsRelationships.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/BatmanBeginsRelationships.png)

You can delete a relationship between nodes by first finding it in the graph and then deleting it.

In this example, we want to delete the ACTED_IN relationship between Christian Bale and the movie Batman Begins. We find the relationship, and then delete it:

```javascript
MATCH (a:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE a.name = 'Christian Bale' AND m.title = 'Batman Begins'
DELETE rel
RETURN a, m
```

Here is the result of running this Cypher statement:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/DeleteRelationship.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/DeleteRelationship.png)

In this example, we find the node for the producer, Benjamin Melniker, as well as his relationship to movie nodes. First, we delete the relationship(s), then we delete the node:

```javascript
MATCH (p:Person)-[rel:PRODUCED]->(:Movie)
WHERE p.name = 'Benjamin Melniker'
DELETE rel, p
```

### Deleting nodes and relationships

The most efficient way to delete a node and its corresponding relationships is to specify DETACH DELETE. When you specify DETACH DELETE for a node, the relationships to and from the node are deleted, then the node is deleted.

If were were to attempt to delete the Liam Neeson node without first deleting its relationships:

```javascript
MATCH (p:Person)
WHERE p.name = 'Liam Neeson'
DELETE p
```

We would see this error:

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/LiamNeesonDeleteError.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/LiamNeesonDeleteError.png)

Here we delete the Liam Neeson node and its relationships to any other nodes:

```javascript
MATCH (p:Person)
WHERE p.name = 'Liam Neeson'
DETACH DELETE  p
```

### Exercise 10: Deleting Nodes and Relationships

In the query edit pane of Neo4j Browser, execute the browser command: `:play intro-neo4j-exercises` and follow the instructions for Exercise 10.

```javascript
// Exercise 10.1: Delete a relationship.
// Recall that in the graph we have been working with, we have the HELPED relationship between Tom Hanks and Gary Sinise. We have decided that we no longer need this relationship in the graph. Delete the HELPED relationship from the graph.
MATCH (:Person)-[rel:HELPED]-(:Person)
DELETE rel

// Exercise 10.2: Confirm that the relationship has been deleted.
MATCH (:Person)-[rel:HELPED]-(:Person)
RETURN rel

// Exercise 10.3: Retrieve a movie and all of its relationships.
MATCH (p:Person)-[rel]-(m:Movie)
WHERE m.title = 'Forrest Gump'
RETURN p, rel, m

// Exercise 10.4: Try deleting a node without detaching its relationships.
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
DELETE m
// Neo.ClientError.Schema.ConstraintValidationFailed
// Cannot delete node<180>, because it still has relationships. To delete this node, you must first delete its relationships.

// Exercise 10.5: Delete a Movie node, along with its relationships.
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
DETACH DELETE m

// Exercise 10.6: Confirm that the Movie node has been deleted.
MATCH (p:Person)-[rel]-(m:Movie)
WHERE m.title = 'Forrest Gump'
RETURN p, rel, m
```

## Merging data in the graph

Thus far, you have learned how to create nodes, labels, properties, and relationships in the graph. You can use MERGE to either create new nodes and relationships or to make structural changes to existing nodes and relationships.

For example, how the graph engine behaves when a duplicate element is created depends on the type of element:

| If you use CREATE:  | The result is:
| ---                 | ---
| Node                | If a node with the same property values exists, a duplicate node is created.
| Label               | If the label already exists for the node, the node is not updated.
| Property            | If the node or relationship property already exists, it is updated with the new value.
|                     | Note: If you specify a set of properties to be created using = rather than +=, it could remove existing properties if they are not included in the set.
| Relationship        | If the relationship exists, a duplicate relationship is created.

WARNING: You should never create duplicate nodes or relationships in a graph.

The MERGE clause is used to find elements in the graph. But if the element is not found, it is created.

You use the MERGE clause to:

+ Create a unique node based on label and key information for a property and if it exists, optionally update it.
+ Create a unique relationship.
+ Create a node and relationship to it uniquely in the context of another node.

### Using MERGE to create nodes

Here is the simplified syntax for the MERGE clause for creating a node:

```javascript
MERGE (variable:Label{nodeProperties})
RETURN variable
```

If there is an existing node with Label and nodeProperties found in the graph, no node is created. If, however the node is not found in the graph, then the node is created.

When you specify nodeProperties for MERGE, you should only use properties that satisfy some sort of uniqueness constraint. You will learn about uniqueness constraints in the next module.

Here we use MERGE to find a node with the Actor label with the key property name of Michael Caine, and we set the born property to 1933. Our data model has never used the label, Actor so this is a new entity type in our graph:

```javascript
MERGE (a:Actor {name: 'Michael Caine'})
SET a.born = 1933
RETURN a
```

If we had a node labeled `Person` with the same name value, this MERGE statement would create a new node.

NOTE: When you specify the node to merge, you should only use properties that have a unique index. You will learn about uniqueness later in this training.

### Using MERGE to create relationships

Here is the syntax for the MERGE clause for creating relationships:

```javascript
MERGE (variable:Label {nodeProperties})-[:REL_TYPE]->(otherNode)
RETURN variable
```

If there is an existing node with Label and nodeProperties with the :REL_TYPE to otherNode found in the graph, no relationship is created. If the relationship does not exist, it is created.

Although, you can leave out the direction of the relationship being created with the MERGE, in which case a left-to-right arrow will be assumed, a best practice is to always specify the direction of the relationship. However, if you have bidirectional relationships and you want to avoid creating duplicate relationships, you must leave off the arrow.

### Specifying creation behavior when merging

You can use the MERGE clause, along with ON CREATE to assign specific values to a node being created as a result of an attempt to merge.

Here is an example where create a new node, specifying property values for the new node:

```javascript
MERGE (a:Person {name: 'Sir Michael Caine'})
ON CREATE SET a.birthPlace = 'London',
              a.born = 1934
RETURN a
```

We know that there are no existing Sir Michael Caine Person nodes. When the MERGE executes, it will not find any matching nodes so it will create one and will execute the ON CREATE clause where we set the birthplace and born property values.

You can also specify an ON MATCH clause during merge processing. If the exact node is found, you can update its properties or labels. Here is an example:

```javascript
MERGE (a:Person {name: 'Sir Michael Caine'})
ON CREATE SET a.born = 1934,
              a.birthPlace = 'UK'
ON MATCH SET a.birthPlace = 'UK'
RETURN a
```

### Using MERGE to create relationships

Using MERGE to create relationships is expensive and you should only do it when you need to ensure that a relationship is unique and you are not sure if it already exists.

In this example, we use the MATCH clause to find all Person nodes that represent Michael Caine and we find the movie, Batman Begins that we want to connect to all of these nodes. We already have a connection between one of the Person nodes and the Movie node. We do not want this relationship to be duplicated. This is where we can use MERGE as follows:

```javascript
MATCH (p:Person), (m:Movie)
WHERE m.title = 'Batman Begins' AND p.name ENDS WITH 'Caine'
MERGE (p)-[:ACTED_IN]->(m)
RETURN p, m
```

You must be aware of the behavior of the MERGE clause and how it will automatically create nodes and relationships. MERGE tries to find a full pattern and if it doesn’t find it, it creates that full pattern. That’s why in most cases you should first MERGE your nodes and then your relationship afterwards.

Only if you intentionally want to create a node within the context of another (like a month within a year) then a MERGE pattern with one bound and one unbound node makes sense.

For example:

```javascript
MERGE (fromDate:Date {year: 2018})<-[:IN_YEAR]-(toDate:Date {month: 'January'})
```

### Exercise 11: Merging Data in the graph

In the query edit pane of Neo4j Browser, execute the browser command: `:play intro-neo4j-exercises` and follow the instructions for Exercise 11.

```javascript
// Exercise 11.1: Use MERGE to create a Movie node.
// Use MERGE to create (ON CREATE) a node of type Movie with the title property, Forrest Gump. If created, set the released property to 1994.
MERGE (m:Movie {title: 'Forrest Gump'})
ON CREATE SET m.released = 1994
RETURN m

// Exercise 11.2: Use MERGE to update a node.
// Use MERGE to update (ON MATCH) a node of type Movie with the title property, Forrest Gump. If found, set the tagline property to "Life is like a box of chocolates…​you never know what you’re gonna get.".
MERGE (m:Movie {title: 'Forrest Gump'})
ON CREATE SET m.released = 1994
ON MATCH SET m.tagline = "Life is like a box of chocolates...you never know what you're gonna get."
RETURN m

// Exercise 11.3: Use MERGE to create a Production node.
// Use MERGE to create (ON CREATE) a node of type Production with the title property, Forrest Gump. If created, set the property year to the value 1994.
MERGE (p:Production {title: 'Forrest Gump'})
ON CREATE SET p.year = 1994
RETURN p

// Exercise 11.4: Find all labels for nodes with a property value.
// Query the graph to find labels for nodes with the title property, Forrest Gump.
MATCH (m)
WHERE m.title = 'Forrest Gump'
RETURN  labels(m)

// Exercise 11.5: Use MERGE to update a Production node.
// Use MERGE to update (ON MATCH) the existing Production node for Forrest Gump to add the company property with a value of Paramount Pictures.
MERGE (p:Production {title: 'Forrest Gump'})
ON MATCH SET p.company = 'Paramount Pictures'
RETURN p

// Exercise 11.6: Use MERGE to add a label to a node.
// Use MERGE to add the OlderMovie label to the movie, Forrest Gump.
MERGE (m:Movie {title: 'Forrest Gump'})
ON MATCH SET m:OlderMovie
RETURN labels(m)

// Exercise 11.7: Use MERGE to create two nodes and a single relationship.
//  Execute the following Cypher statement that uses MERGE to create two nodes and a single relationship
MERGE (p:Person {name: 'Robert Zemeckis'})-[:DIRECTED]->(m {title: 'Forrest Gump'})
// This statement first finds all Person nodes that have only the name property value of Robert Zemeckis. It then finds all nodes with only the title property set to Forrest Gump. There are no Person or other nodes that have only these properties so the graph engine creates them. Then the graph engine creates the relationship between these two nodes. That is, this MERGE operation creates two nodes and a single relationship. If we had provided all of the property values for the nodes, we would not have created the extra nodes.
//
// In fact, you should never create nodes and relationships together like this! This example is here to show you how powerful Cypher can be. A best practice is to create nodes first, then relationships.

// Exercise 11.8: Use the same MERGE statement to attempt to create two nodes and a single relationship.
// Repeat the execution of the previous statement

// Exercise 11.9: Find the correct Person node to delete.
MATCH (p:Person {name: 'Robert Zemeckis'})-[rel]-(x)
WHERE NOT EXISTS (p.born)
RETURN p, rel, x

// Exercise 11.10: Delete this Person node, along with its relationships.
MATCH (p:Person {name: 'Robert Zemeckis'})--()
WHERE NOT EXISTS (p.born)
DETACH DELETE p

// Exercise 11.11: Find the correct Forrest Gump node to delete.
MATCH (m)
WHERE m.title = 'Forrest Gump' AND labels(m) = []
RETURN m, labels(m)

// Exercise 11.12: Delete the Forrest Gump node.
// It should have no relationships associated with it, but if it did for some reason we could use DETACH DELETE as a safeguard
MATCH (m)
WHERE m.title = 'Forrest Gump' AND labels(m) = []
DETACH DELETE m

// Exercise 11.13: Use MERGE to create the DIRECTED relationship.
// Use MERGE to create the DIRECTED relationship between Robert Zemeckis and the Movie, Forrest Gump.
MATCH (p:Person), (m:Movie)
WHERE p.name = 'Robert Zemeckis' AND m.title = 'Forrest Gump'
MERGE (p)-[:DIRECTED]->(m)

// Exercise 11.14: Use MERGE to create the ACTED_IN relationship.
// Use MERGE to create the ACTED_IN relationship between the actors, Tom Hanks, Gary Sinise, and Robin Wright and the Movie, Forrest Gump.
MATCH (p:Person), (m:Movie)
WHERE p.name IN ['Tom Hanks','Gary Sinise', 'Robin Wright']
      AND m.title = 'Forrest Gump'
MERGE (p)-[:ACTED_IN]->(m)

// Exercise 11.15: Modify the role relationship property.
// Modify the relationship property, role for their roles in Forrest Gump:
//   Tom Hanks is Forrest Gump
//   Gary Sinise is Lt. Dan Taylor
//   Robin Wright is Jenny Curran
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE m.title = 'Forrest Gump'
SET rel.roles =
CASE p.name
  WHEN 'Tom Hanks' THEN ['Forrest Gump']
  WHEN 'Robin Wright' THEN ['Jenny Curran']
  WHEN 'Gary Sinise' THEN ['Lt. Dan Taylor']
END
```

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
