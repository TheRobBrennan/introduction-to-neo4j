# Welcome

This folder has been created to track my work and ideas while studying [Setting Up Your Development Environment](https://neo4j.com/graphacademy/online-training/introduction-to-neo4j/part-3/)

For my own learning purposes, I will include sections of text from the original course to solidify my own understanding of the concepts presented.

## About this module

As a developer, you will create Neo4j Databases, add and update data in them, and query the data. When you learn to use Neo4j as a developer, you have three options⎼ Neo4j Desktop, Neo4j Aura, or Neo4j Sandbox. In this module you will learn how to use each of these development environments and select the option that is best for your needs while you are learning about Neo4j.

Many graph-enabled applications have been developed and deployed using Neo4j’s Community Edition (free). If your enterprise requires production features such as failover, clustering, monitoring, advanced access control, secure routing, etc. you will use Neo4j’s Enterprise Edition, or you can use Neo4j Aura which is the cloud instance of a Neo4j Enterprise Edition Database.

## Neo4j Desktop

The Neo4j Desktop includes the Neo4j Database server which has a graph engine and kernel so that Cypher statements can be executed to access a database on your system. It includes an application called Neo4j Browser. Neo4j Browser enables you to access a Neo4j database using Cypher. You can also call built-in procedures that communicate with the database server. There are a number of additional libraries and drivers for accessing the Neo4j database from Cypher or from another programming language that you can install in your development environment. If you are looking to use your system for application development and you want to be able to create multiple Neo4j databases on your machine, you should consider downloading the Neo4j Desktop (free download). The Neo4j Desktop runs on OS X, Linux, and Windows.

## Neo4j Aura

Neo4j Aura enables you to create a Neo4j Database instance in the Cloud using a monthly subscription model. The amount per month depends on the amount of memory required for the database. This frees you from needing to install Neo4j on your system. Once you create a Neo4j Database at the [Neo4j Aura site](https://neo4j.com/aura/), it will be managed by Neo4j. Backups are done automatically for you and the database is available 24X7. In addition, the Neo4j will ensure that the database instance is always up-to-date with the latest version of Neo4j.

## Neo4j Sandbox

The Neo4j Sandbox is another way that you can begin development with Neo4j. It is a temporary, cloud-based instance of a Neo4j Server with its associated graph that you can access from any Web browser. The database in a Sandbox may be blank or it may be pre-populated. It is started automatically for you when you create the Sandbox.

By default, the Neo4j Sandbox is available for three days, but you can extend it for up to 10 days. If you do not want to install Neo4j Desktop on your system, consider creating a Neo4j Sandbox. You must make sure that you extend your lease of the Sandbox, otherwise you will lose your graph and any saved Cypher scripts you have created in the Sandbox. However, you can use Neo4j Browser Sync to save Cypher scripts from your Sandbox. We recommend you use the Neo4j Desktop or Neo4j Aura for a real development project. The Sandbox is intended as a temporary environment or for learning about the features of Neo4j as well as specific graph use-cases.

## Steps for setting up your development environment for this training

## Guided Exercise: Getting Started with Neo4j Desktop

If you want to download and install Neo4j Desktop on your system, follow along with one of these videos to download, install and get started using Neo4j Desktop. If you will be using Neo4j Desktop in your development environment, you can follow the steps in the video to create a TestMovies project with its corresponding Movies database.

For macOS - [WATCH: Getting Started with Neo4j Desktop V1.2.3 on OS X ~7:48](https://www.youtube.com/watch?v=pPhJi9twN9Q)

## Guided Exercise: Creating a Database in Neo4j Aura

## Guided Exercise: Creating a Neo4j Sandbox

## Using Neo4j Browser

The data returned is typically visualized as nodes and relationships in a graph, but can also be displayed as tables.

In addition to executing Cypher statements, you can execute a number of system calls that are related to the database being accessed by the Browser. For example, you can retrieve the list of queries that are currently running in the server.

## Guided Exercise: Getting Started with Neo4j Browser

## Check your understanding

### Question 1: What development environment should you use if you want to develop a graph-enabled application using a local Neo4j Database?

[ x ] Neo4j Desktop
[ ] Neo4j Sandbox

### Question 2: What development environment should you use if you want develop a graph-enabled application using a temporary, cloud-based Neo4j Database?

[ ] Neo4j Desktop
[ x ] Neo4j Sandbox

### Question 3: Which Neo4j Browser command do you use to view a browser guide for the Movie graph?

[ ] MATCH (Movie Graph)
[ ] :MATCH (Movie Graph)
[ ] play Movie Graph
[ x ] :play Movie Graph
