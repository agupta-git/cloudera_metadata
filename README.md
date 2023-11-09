# Metadata in Cloudera Data Warehouse

## Overview
Purpose of this article is to explain a few ways to access metadata of data objects in Cloudera's Hive & Impala data warehouses.

Before we get into the specific details, let's understand the typical way to access metadata and data objects in Cloudera Data Platform.
![image](https://github.com/agupta-git/cloudera_metadata/assets/2523891/7a7d4836-d6cb-4454-9a16-36e0e966833a)
Hive Metastore (HMS) is 

Now, let's understand all of this in the context of CDP form-factors.
![image](https://github.com/agupta-git/cloudera_metadata/assets/2523891/25032d0d-5a6d-4232-a46c-acbef002dcfe)

## Ways to access metadata
Below are a few ways to access metadata of data objects in Cloudera's Hive & Impala data warehouses.
1. HMS
2. sys database
   include SQL queries
3. Atlas API (https://pypi.org/project/apache-atlas/)
4. Direct access within the cluster
