# Welcome

This folder has been created to track my work and ideas while studying [Introduction to Neo4j](https://neo4j.com/graphacademy/online-training/introduction-to-neo4j/part-2/)

For my own learning purposes, I will include sections of text from the original course to solidify my own understanding of the concepts presented.

## Neo4j Graph Platform

### Neo4j Database

### Neo4j Database: Index-free adjacency

With index free adjacency, when a node or relationship is written to the database, it is stored in the database as connected and any subsequent access to the data is done using pointer navigation which is very fast. Since Neo4j is a native graph database (i.e. it has a graph as its core data model), it supports very large graphs where connected data can be traversed in constant time without the need for an index.

### Neo4j Database: ACID (Atomic, Consistent, Isolated, Durable)

Transactionality is very important for robust applications that require an ACID (atomicity, consistency, isolation, and durability) guarantees for their data. If a relationship between nodes is created, not only is the relationship created, but the nodes are updated as connected. All of these updates to the database must all succeed or fail.

### Clusters

Neo4j supports clusters that provide high availablity, scalability for read access to the data, and failover which is important to many enterprises.

### Graph engine

### Language and driver support

Neo4j supports Java, JavaScript, Python, C#, and Go drivers out of the box that use Neo4j’s bolt protocol for binary access to the database layer. Bolt is an efficient binary protocol that compresses data sent over the wire as well as encrypting the data. For example, you can write a Java application that uses the Bolt driver to access the Neo4j database, and the application may use other packages that allow data integration between Neo4j and other data stores or uses as common framework such as spring.

### Libraries

Neo4j supports Java, JavaScript, Python, C#, and Go drivers out of the box that use Neo4j’s bolt protocol for binary access to the database layer. Bolt is an efficient binary protocol that compresses data sent over the wire as well as encrypting the data. For example, you can write a Java application that uses the Bolt driver to access the Neo4j database, and the application may use other packages that allow data integration between Neo4j and other data stores or uses as common framework such as spring.

### Tools

In a development environment, you will use the Neo4j Browser or a Web browser to access data and test your Cypher statements, most of which will be used as part of your application code. Neo4j Browser is an application that uses the JavaScript Bolt driver to access the graph engine of the Neo4j database server. Neo4j also has a new tool called Bloom that enables you to visualize a graph without knowing much about Cypher. In addition, there are many tools for importing and exporting data between flat files and a Neo4j Database, as well as an ETL tool.

In this video, you can see how Neo4j Bloom can be used to examine and modify a Graph, even when you know very little about Cypher:

🔥[WATCH: Neo4j Bloom: Investigating Patterns in Financial Transactions ~12:36](https://www.youtube.com/watch?v=KjINhGbG-So)🔥

### Whiteboard modeling

With a property graph model, it is very easy to collaborate with colleagues to come up with a whiteboard model of your data that is easy to understand and easy modify. You then use the model to create the nodes, relationships, labels, and properties you will use for your Neo4j data. Even after the graph has been defined and populated with data, it is easy to modify the graph as your application needs change.

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/Whiteboard1.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/Whiteboard1.png)

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/Whiteboard2.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/Whiteboard2.png)

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/Whiteboard3.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/Whiteboard3.png)

## Neo4j Graph Platform architecture

![https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/Neo4jPlatform.png](https://s3-us-west-1.amazonaws.com/data.neo4j.com/intro-neo4j/img/Neo4jPlatform.png)

Here is the big picture of the Neo4j Graph Platform. The Neo4j Database provides support for graph transactions and analytics. Developers use the Neo4j Desktop, along with Neo4j Browser to develop graphs and test them, as well as implement their applications in a number of languages using supported drivers, tools and APIs. Administrators use tools to manage and monitor Neo4j Databases and clusters. Business users use out-of-the box graph visualization tools or they use custom tools. Data analysts and scientists use the analytics capabilities in the Graph Algorithm libraries or use custom libraries to understand and report findings to the enterprise. Applications can also integrate with existing databases (SQL or NoSQL), layering Neo4j on top of them to provide rich, graph-enabled access to the data.

## Check your understanding

### Question 1: What are some of the benefits provided by the Neo4j Graph Platform?

[ x ] Database clustering
[ x ] ACID
[ x ] Index free adjacency
[ x ] Optimized graph engine

### Question 2: What libraries are available for the Neo4j Graph Platform?

[ x ] APOC
[   ] JGraph
[ x ] Graph Algorithms
[ x ] GraphQL

### Question 3: What are some of the language drivers that come with Neo4j out of the box?

[ x ] Java
[   ] Ruby
[ x ] Python
[ x ] JavaScript
