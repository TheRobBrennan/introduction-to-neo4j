# Welcome

This folder has been created to track my work and ideas while studying [Introduction to Graph Databases](https://neo4j.com/graphacademy/online-training/introduction-to-neo4j/part-1/)

For my own learning purposes, I will include sections of text from the original course to solidify my own understanding of the concepts presented.

## The evolution of graph databases

[WATCH: Intro to Graph Databases Episode #1 - Evolution of DBs](https://www.youtube.com/watch?list=PL9Hl4pk2FsvWM9GWaguRhlCQ-pa-ERd4U&v=5Tl8WcaqZoc)

## What Is a graph database?

Graph databases are generally built for use with online transaction processing (OLTP) systems. Accordingly, they are normally optimized for transactional performance, and engineered with transactional integrity and operational availability in mind.

Unlike other databases, relationships take first priority in graph databases. This means your application doesn’t have to infer data connections using foreign keys or out-of-band processing, such as MapReduce.

## The case for graph databases

[WATCH: Intro to Graph Databases Episode #2 - Properties of Graph DBs & Use Cases](https://www.youtube.com/watch?list=PL9Hl4pk2FsvWM9GWaguRhlCQ-pa-ERd4U&v=-dCeFEqDkUI)

## What is a graph?

A graph is composed of two elements: **nodes** and **relationships**.

Each node represents an entity (a person, place, thing, category or other piece of data). With Neo4j, nodes can have labels that are used to define types for nodes. For example, a Location node is a node with the label Location. That same node can also have a label, Residence. Another Location node can also have a label, Business. A label can be used to group nodes of the same type. For example, you may want to retrieve all of the Business nodes.

Each relationship represents how two nodes are connected. For example, the two nodes Person and Location, might have the relationship LIVES_AT pointing from a Person node to Location node. A relationship represents the verb or action between two entities. The MARRIED relationship is defined from one Person node to another Person node. Although the relationship is defined as directional, it can be queried in a non-directional manner. That is, you can query if two Person nodes have a MARRIED relationship, regardless of the direction of the relationship. For some data models, the direction of the relationship is significant. For example, in Facebook, using the KNOWS relationship is used to indicate which Person invited the other Person to be a friend.

This general-purpose structure allows you to model all kinds of scenarios: from a system of roads, to a network of devices, to a population’s medical history, or anything else defined by relationships.

The Neo4j database is a property graph. You can add properties to nodes and relationships to further enrich the graph model.

This enables you to closely align data and connections in the graph to your real-world application. For example, a Person node might have a property, name and a Location node might have a property, address. In addition, a relationship, MARRIED , might have a property, since.

[WATCH: Intro to Graph Databases Episode #3 - Property Graph Model](https://www.youtube.com/watch?list=PL9Hl4pk2FsvWM9GWaguRhlCQ-pa-ERd4U&v=NH6WoJHN4UA)

## Modeling relational to graph

Many applications’ data is modeled as relational data. There are some similarities between a relational model and a graph model:

| Relational  | Graph
|---          |---
| Rows        | Nodes
| Joins       | Relationships
| Table names | Labels
| Columns     | Properties

But, there are some ways in which the relational model differs from the graph model:

| Relational                            | Graph
|---                                    |---
| Each column must have a field value.  | Nodes with the same label aren’t required to have the same set of properties.
| Joins are calculated at query time.   | Relationships are stored on disk when they are created.
| A row can belong to one table.        | A node can have many labels.

## Run-time behavior: RDBMS vs graph

## How we model: RDBMS vs graph

How you model data from relational vs graph differs:

| Relational                                                                      | Graph
|---                                                                              |---
| Try and get the schema defined and then make minimal changes to it after that.  | It’s common for the schema to evolve with the application.
| More abstract focus when modeling i.e. focus on classes rather than objects.    | Common to use actual data items when modeling.

## How does Neo4j support the property graph model?

## Check your understanding

### Question 1: What elements make up a graph?

[ x ] nodes
[ x ] relationships
[ ] tuples
[ ] documents

### Question 2: Suppose that you want to create a graph to model customers, products, what products a customer buys, and what products a customer rated. You have created nodes in the graph to represent the customers and products. In this graph, what relationships would you define? Select the correct answers.

[ x ] BOUGHT
[ x ] RATED
[ ] IS_A_CUSTOMER
[ ] IS_A_PRODUCT

### Question 3: What query language is used with a Neo4j Database?

[ x ] Cypher
[ ] SQL
[ ] CQL
[ ] OPath
