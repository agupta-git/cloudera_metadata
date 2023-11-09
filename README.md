# Metadata in Cloudera data warehouses

## Overview
Purpose of this article is to explain a few ways to access metadata of data objects in Cloudera's Hive & Impala data warehouses.

Before we get into the specific details, let's understand a typical method to access metadata and data objects in Cloudera Data Platform.

![image](https://github.com/agupta-git/cloudera_metadata/assets/2523891/7a7d4836-d6cb-4454-9a16-36e0e966833a)

Hive Metastore (HMS) database holds the **metadata** of all Hive & Impala data objects. When user/application is outside the Cloudera cluster, thrift protocol is used to access it.  
Hive & Impala data warehouses hold all the **data objects**. When user/application is outside the Cloudera cluster, Cloudera's JDBC/ODBC [drivers](https://www.cloudera.com/downloads.html) are used to access it.
<hr>

As you know Cloudera excels at Hybrid architecture and there is a consistent method of accessing metadata and data across different deployment models. Let's now understand how it works. 
> If you're not already familiar with the CDP deployment models, it's highly recommended to check out the following links: [Base](https://docs.cloudera.com/cdp-private-cloud-base/7.1.8/index.html), [Datahub](https://docs.cloudera.com/data-hub/cloud/index.html), [CDW Data Service in Private Cloud](https://docs.cloudera.com/data-warehouse/1.5.0/index.html) and [CDW Data Service in Public Cloud](https://docs.cloudera.com/data-warehouse/cloud/index.html).

![image](https://github.com/agupta-git/metadata_cloudera_dw/assets/2523891/35de5ab4-f84b-4e31-8ed1-f623d2021af4)

A single HMS database is available per environment (_environment refers to CDP's On-Premise or Public Cloud installation_), to hold information about all the Hive/Impala objects. In other words, regardless of where Hive/Impala objects are created (ex: CDW Data Service), their metadata is going to be available in the single HMS instance that's associated with the environment.

To access HMS database outside the cluster in both CDP On-Prem and Public Cloud, thrift protocol is used along with the Kerberos authentication mechanism. We must configure cross-realm Kerberos trust between the user/application and Cloudera environment, so they're able to communicate with each other.

## Ways to access metadata
Below are a few ways to access metadata of data objects in Cloudera's Hive & Impala data warehouses --
1. ### Hive Metastore (HMS)  

   HMS database holds the **metadata** of all Hive & Impala data objects.

   If you're accessing it outside the Cloudera cluster, use thrift protocol and ensure cross-realm Kerberos trust is configured between the client (user/application) and Cloudera cluster. This is typically done by adding krb5.conf to the client.

   If you're accessing it within the Cloudera cluster, use the HMS instance that you setup during CDP On-Premise installation or if you're in Public Cloud then use the instructions [here](https://community.cloudera.com/t5/Community-Articles/Accessing-Hive-Metastore-DB-on-CDP-Public-Cloud/ta-p/338590).
   
3. ### sys database

   `sys` database mirrors HMS database and resides in Hive data warehouse. Hive data warehouse is accessed using Cloudera's JDBC/ODBC drivers.  
   Note that even though `sys` database is only available through Hive data warehouse, it contains the metadata of both Hive & Impala data objects. For reference, see an example query below to retrieve the metadata.

   ```
   -- See a few details about a subset of tables
   select b.name, a.tbl_name, a.owner, a.tbl_type, c.location, a.view_original_text, a.view_expanded_text
   from sys.tbls a
   join sys.dbs b on b.db_id = a.db_id
   join sys.sds c on c.sd_id = a.sd_id
   where tbl_name like '%tmp_%';
   ```

   ![Screen Shot 2023-07-19 at 1 47 31 PM](https://github.com/agupta-git/metadata_cloudera_dw/assets/2523891/a9c443b0-feb6-4b6b-9bd8-2bc474ac55a7)

  > Tip: keep [this HMS schema](https://analyticsanvil.wordpress.com/2016/08/21/useful-queries-for-the-hive-metastore/) handy when querying the sys database.
   
3. ### Apache Atlas API
   [Apache Atlas](https://atlas.apache.org/#/) is a governance tool that lets you see the metadata of all data assets, lineage of data and much more. [REST APIs](https://atlas.apache.org/api/v2/index.html) are exposed to retrieve the necessary information remotely. There is also [Python library for Apache Atlas](https://pypi.org/project/apache-atlas/) available on pypi.
   Below is a common curl example of accesssing Atlas in Cloudera using REST API:
   ```
   curl -k -u <usr>:<pwd> https://<cloudera_datalake_host>:443/<env>/cdp-proxy-api/atlas/api/atlas/v2/types/typedefs/
   ```
   Note that full Atlas API URL is available in the Endpoints section of the datalake. See below for reference.

   ![Screen Shot 2023-11-09 at 1 23 47 PM](https://github.com/agupta-git/metadata_cloudera_dw/assets/2523891/8e0f8187-9be0-436c-b0be-2a8430702c8d)

## Summary
Glad you made it this far. Even with the complexity that comes with the Hybrid Architecture, Cloudera has made it simple to access the metadata of data objects in Cloudera's data warehouses. There are multiple options available as discussed above, that customers & partners can choose from based on their requirements.
