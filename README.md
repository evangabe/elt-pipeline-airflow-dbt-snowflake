Building a Extract, Load, Transform (ELT) Pipeline with Airflow, dbt, Snowflake and Docker
==========================================================================================

When an existing company seeks to build out new machine learning services, it can be difficult or even impossible to reroute the flow of data for processing. 
The Extract, Load, and Transform (ELT) approach to this problem implements data processing after data storage, allowing companies to retain their traditional storage solutions (i.e. SQL databases, S3 Buckets, etc...).
This new data processing paradigm, however, requires data scientists and data engineers to maintain a slew of complex subprocesses, from scheduling and triggering of processing tasks to handling errors and storing metadata.
Instead, data scientists and data engineers turn to Apache Airflow: an open-source, distributed data workflow orchestrator.

Apache Airflow represents data workflows (i.e. serving data to a weather app) as a graph of serially-executed dependent tasks (i.e. Task #1 -> Fetch temperature, humidity, ... Task #2 -> Clean data. Task #3 -> Push data to dashboard.). For mathematicians and engineers, a "graph of serially-executed dependent tasks" is euphemism for a "Directed Acyclic Graph" commonly referred to by its acronym, DAG. Airflow's magic is thanks to these DAGs, whose constraint of non-circularity ensures that tasks never get locked up. To get a data workflow up-and-running, developers write code for each of the workflow tasks in the DAG, then they set the order of task execution. Developers can optionally schedule when the Airflow DAG should first execute and on what interval it should rerun. It's that easy!

Project Overview
========

In this project, I demonstrate how to use Airflow in conjunction with dbt to automate data warehousing in Snowflake. While Airflow manages the graph representation of the data workflow, dbt establishes the connection to Snowflake and compartmentalizes data transformations in SQL. I'm using Docker for spinning up Airflow and running dbt locally, and am using Python for programming and `pip` as the package manager.


Project Contents
================

This repository contains the following files and folders:

- dags: This folder contains the Python files for the Airflow DAG. By default, this directory includes one DAG:
    - `dbt_dag`: This DAG configures the dbt project and establishes a connection to my Snowflake profile
    - `dbt`: This folder contains the dbt project, labeled `data_pipeline`, which dictates the order of execution for all SQL data transformations that will run in Snowflake.
- Dockerfile: This file contains a versioned Astro Runtime Docker image and `pip` installation step. If you want to learn more about Astro, see their [Getting Started Tutorial](https://www.astronomer.io/docs/learn/get-started-with-airflow)
- include: This folder contains any additional files that you want to include as part of your project. It is empty by default.
- packages.txt: Install OS-level packages needed for your project by adding them to this file. It is empty by default.
- requirements.txt: Install Python packages needed for your project by adding them to this file. It is empty by default.
- plugins: Add custom or community plugins for your project to this file. It is empty by default.
- airflow_settings.yaml: Use this local-only file to specify Airflow Connections, Variables, and Pools instead of entering them in the Airflow UI as you develop DAGs in this project.

Deploy Your Own Version Locally
===============================

1. Start Airflow on your local machine by running 'astro dev start'.

This command will spin up 4 Docker containers on your machine, each for a different Airflow component:

- Postgres: Airflow's Metadata Database
- Webserver: The Airflow component responsible for rendering the Airflow UI
- Scheduler: The Airflow component responsible for monitoring and triggering tasks
- Triggerer: The Airflow component responsible for triggering deferred tasks

2. Verify that all 4 Docker containers were created by running 'docker ps'.

Note: Running 'astro dev start' will start your project with the Airflow Webserver exposed at port 8080 and Postgres exposed at port 5432. If you already have either of those ports allocated, you can either [stop your existing Docker containers or change the port](https://www.astronomer.io/docs/astro/cli/troubleshoot-locally#ports-are-not-available-for-my-local-airflow-webserver).

3. Access the Airflow UI for your local Airflow project. To do so, go to http://localhost:8080/ and log in with 'admin' for both your Username and Password.

You should also be able to access your Postgres Database at 'localhost:5432/postgres'.