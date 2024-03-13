---
title: "Analytics on S3 Data lake using AWS Glue Crawler and Amazon Athena"
seoTitle: "Building Data Lake Analytics with AWS Glue Crawler and Amazon Athena"
seoDescription: "Explore the power of AWS Glue and AWS Athena in data analytics on the AWS platform. "
datePublished: Sun Dec 03 2023 06:15:00 GMT+0000 (Coordinated Universal Time)
cuid: cltpfybbw00020al79bb66spi
slug: analytics-on-s3-data-lake-using-aws-glue-crawler-and-amazon-athena
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1710310675038/8121bb41-92a7-41b1-aac3-2c1bc0302777.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1710312456642/36f9bc5a-0928-4866-a803-9540d7b76d80.png
tags: aws, data-science, data-engineering, aws-glue, aws-athena

---

## Introduction

In today's world, data is growing at an exponential rate. While this data holds immense value and has the potential to impact various aspects of our world, extracting value from such large datasets is no simple task. This is where data analytics comes in. Data analytics is a process used in the field of data science to examine large datasets, uncovering hidden patterns, correlations, trends, insights, and relationships. These insights can help organizations gain a competitive edge in the market by making data-driven decisions, improving operational efficiency, and identifying new business opportunities. The main tasks involved in data analytics include data collection, cleaning, processing, and analysis.

In my previous [blog](https://anish-shilpakar.com.np/unleashing-the-power-of-elt-in-aws-using-airbyte), I discussed ELT and data lakes, highlighting their importance in the current big data world. This blog continues that discussion by showcasing the power of AWS tools like AWS Glue and AWS Athena for illustrating data analytics on the AWS platform. But before we delve into that, let's first understand what AWS Glue and AWS Athena are and why they are used in data engineering. AWS Glue is a serverless data integration service that simplifies the process of data discovery, preparation, movement, and integration from multiple sources. It offers data crawlers that can automatically infer schema from data stored in data lakes like S3 buckets and create a centralized data catalog. The Glue data catalog serves as a metastore containing metadata for all the crawled tables. Additionally, AWS Glue allows us to visually create ETL pipelines for transformation and use Spark notebooks to process and transform data. Similarly, Amazon Athena is an interactive query service that enables us to analyze data using standard SQL. Amazon Athena offers two options for analyzing data: using the distributed query engine "Trino" to analyze data in S3, on-premises, or other clouds, or using notebooks to build interactive Spark applications and analyze data using "PySpark" and "Spark SQL".

In this blog, I will be using the Trino query engine of Amazon Athena to analyze the data in the Glue database, which was ingested from the S3 data lake using AWS Glue data crawlers. The architecture for this implementation is explained below in the Architecture section.

## Architecture

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710310709457/9ad49927-4563-4781-99bc-30a85ed938ed.png align="center")

### About AWS Glue

AWS Glue is a serverless data integration solution widely used for data analytics in AWS. AWS Glue enables us to connect with over 70 diverse data sources and to manage data in a centralized data catalog. It offers flexible support for different workloads like ETL, ELT, and streaming all in one service and offers services like data discovery, modern ETL, data cleansing, data transformation, and centralized cataloging. The following are some of the main components of AWS Glue

1. AWS Glue Data Catalog
    

AWS Glue data catalog is a fully managed centralized metadata repository and catalog service that stores metadata like table definitions, column names, data types, and other attributes that describe the data used in ETL jobs created in AWS Glue. It helps to simplify and automate the process of data discovery and cataloging, providing a unified view of data across different AWS services and data sources, and making it easier for users to query and analyze the data without having to know the exact location or format of data while still maintaining the data consistency.

2. AWS Glue Crawler
    

AWS Glue Crawler is a feature of AWS Glue that automatically discovers the schema of data in the source and stores the metadata in the AWS Glue Data Catalog. It automates the process of cataloging data making it easier for users to analyze their data without the need to manually define schema for each dataset in the source. AWS Glue crawlers run classifiers which may either be built-in or custom classifiers created by users which automates the process of inferring schema from the dataset saving time and effort for users and allowing them to focus on data analysis and deriving insights from their data.

3. AWS Glue Databases
    

AWS Glue databases are logical containers that store metadata tables in the AWS Glue data catalog. They are used to manage and organize metadata tables which define data from different data stores and make it easy to query data. When a glue data crawler runs on a data source, it will create tables within a specified glue database in the data catalog. Databases help in organizing the tables based on their schema and usage, allowing users to easily locate and access tables they need for analysis and processing.

4. AWS Glue ETL
    

AWS Glue ETL ( Extract, Transform, Load) is a fully managed ETL service provided by AWS that helps users prepare and transform their data for analysis quickly and easily. It simplifies the ETL process by automating tedious tasks like data preparation, data type conversion, and data format conversion and presents itself as a serverless, scalable, and cost-effective solution for data analytics. AWS Glue offers us options for visually creating ETL Jobs as well as writing scripts and spark jobs programmatically for ETL workloads and scheduling the jobs to run as needed. Additionally, it also evaluates and monitors the data quality and provides options for version control by linking the Glue job with a git repository created in AWS Code Commit.

### About Amazon Athena

AWS Athena is an interactive query engine service provided by AWS that is used for analyzing the data stored in data lakes like Amazon S3 using standard SQL queries. AWS Athena is a cost-effective, serverless, and highly scalable service that uses the trino query engine and spark in the backend which can run queries in parallel allowing it to generate fast results even for large datasets and complex queries. AWS Athena uses the AWS glue data catalog to query the data sources for data analysis. It also provides options to visualize the query results by integrating with tools like Amazon Quicksight or third-party BI tools to create dashboards and reports that help us understand and communicate the insights gained from data.

## Implementation Details

This section describes the various steps taken for implementing a simple data analytics pipeline in AWS using AWS Glue and AWS Athena

### Creating AWS Glue Data Crawler

Firstly, navigate to the AWS management console and search for AWS Glue, then open AWS Glue by clicking it.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710310931074/99cb89c6-dfcc-4b7f-a555-d60738a052fa.png align="center")

This opens the Glue console as shown below. Here on the left side, you can see various sections like Data Catalog, Data Integration, and ETL and Legacy pages. For this project, we are only dealing with the AWS Glue data catalog and various components of Glue associated with the AWS Glue data catalog. Here we aim to connect AWS Glue with the AWS S3 data lake which we setup in our last blog and to ingest all the data in that S3 data lake for data analysis in AWS Athena.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710310947101/eb4c8a86-c335-4f13-a449-459ac4d0edff.png align="center")

Firstly we navigate to AWS Glue Crawler and create a crawler.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710310969499/9126769d-057d-41be-806c-b835fa72b91e.png align="center")

Our original data source was SpaceX API and the raw data from this API is stored in our S3 data lake, so let’s name the glue crawler as spacex\_crawler and we can provide a suitable description to the crawler. Also, optionally we can add tags to the cluster. Once done we navigate to the next page.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311041246/fbe6a359-ea19-47a4-a77a-2833ab60b596.png align="center")

On the next page, we need to configure the data source to ingest and catalog the data. Also, for this project, we don’t need to set up a custom classifier as the built-in classifier available in AWS Glue is capable of inferring schema from the CSV files stored in our s3 data lake.

As our data is not already mapped to Glue tables we have to select “Not yet” and click on “Add a data source”

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311053558/33a829fa-8a51-46ba-adf6-8b69c2694c34.png align="center")

Here select S3 as the data source, choose the location of s3 data as in this account, and then click on browse S3 to select the S3 path of airbyte folder which stores all the data that was ingested from airbyte to our S3 data lake. Also select crawl all sub-folders options, as airbyte ingests data from multiple API endpoints and stores them into multiple folders in our S3 bucket. Then select “Add an S3 data source”.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311078420/0db78899-2a71-4824-8650-8a3d141103fd.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311087589/a50f9e83-2fd3-486b-9b0d-7031371a40bc.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311092675/dc9703a7-ce7b-4b65-ab42-48323a8548c6.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311099615/63be7027-ee08-4fca-8232-1e43e476d735.png align="center")

In the next step, we will configure a new IAM role which will provide us necessary access to our S3 buckets. Select the Create new IAM role option inside configure security settings and then provide a suitable name to your IAM role. This automatically creates a new IAM role with all necessary permissions required to access the files from the s3 bucket as shown below. Once the role is configured, click on Next and navigate to the new page.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311113246/17ed9554-b882-4a37-a75a-2751d2354485.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311120370/60ece9cf-5b51-4efd-99ed-46afe5ba2107.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311125140/1a474241-4114-402c-b247-a22e4035f1ff.png align="center")

On the new page, we have to configure output for data and scheduling. Here we define a glue database as our target destination. For this, we will first add a new database by clicking on “Add database” and then provide a suitable name for the database i-e “spacex\_db” in my case.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311138674/907209c1-534e-4cd6-9c72-3c6c79460d1f.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311142802/3f312c16-4aa8-4231-b185-da54d7f50277.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311148205/2aab9816-ebdf-4c39-9a3f-04193166d173.png align="center")

Once created return to the previous page where if we refresh the options, we can see our newly created database. Also, we can optionally configure a table prefix for the metadata tables created inside the glue database. Also in the crawler schedule we have currently selected the “On Demand” option to run the glue crawler only when we need it manually. But according to your need, you can specify a schedule to automate the crawler runs.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311164794/2e6c90ed-48f8-41fb-85f6-1c15222ead27.png align="center")

If you want you can also explore the advanced options available in Glue Crawler.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311177246/19766510-eb2b-48dd-8385-c359ca12ede7.png align="center")

These are the scheduling options available in Glue Crawler.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311190762/4e0bc599-fb82-43e5-9359-1a8bd0957c58.png align="center")

Once done, we will click next.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311200491/9511c9bb-bcde-4bc7-8643-c1e4c4cfc0a3.png align="center")

Finally, we review all the properties and options that we have configured for the AWS Glue data crawler and click on Create to create the glue data crawler. You can now see your crawler in the list of available crawlers.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311207623/5cb9a9c1-f868-413e-be95-ae31acbf4849.png align="center")

Now wait till the status of the glue crawler becomes ready. Once ready click on run crawler and this will start the metadata ingestion and data cataloging process in AWS Glue.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311219237/d7d86955-b525-44f5-9df9-731550c9c03e.png align="center")

The status of the crawler changes to running which means that the crawler is now ingesting the data from the data lake and storing it inside the metadata tables in the glue database. This process takes some time and depends on the amount of data it needs to ingest from the data lake.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311228649/0375078f-5a55-4ec7-a8ff-4091e0264d62.png align="center")

When the crawler run completes successfully, you can see the status of completed in the crawler runs tab. Also, the status of the crawler changes back to ready.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311237656/799eb3e5-644f-4dcf-8b46-a41a3f75a117.png align="center")

Now navigate to the AWS Glue databases page by clicking on the databases option on the right side. Here select the spacex\_db

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311248900/8c031e9b-b7e0-47dc-9a97-6262262c83a9.png align="center")

Inside SpaceX-db we can see the list of all the tables that were created after ingesting data from data lake using the glue crawler. Here we can see the names of tables, the database they belong to, their location in the s3 data lake, and the type of file that contains the original data.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311257854/d174b446-17a0-48ad-95aa-b24a5876ab7b.png align="center")

We can also view the list of metadata tables and inspect their details by navigating to the tables tab in the right-hand menu.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311267767/fe4c7553-6a9a-4de0-b460-66f7558f0969.png align="center")

The successful creation of metadata tables inside the Glue database indicates the successful creation of the AWS Glue data catalog which contains data from our S3 data lake. Now we can use AWS Athena on this glue data catalog to analyze the data stored in our data lake easily.

### Analyzing glue tables using Amazon Athena

For this firstly navigate to the AWS management console and search for Athena, then click on it to open Athena’s console.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311278044/68a5b3e2-2610-4917-992d-e956ed5ffed8.png align="center")

In Athena’s UI click on launch query editor to open the Athena query editor. We can use the query editor to query the tables of the Glue database and perform different analyses.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311285511/a1e29e55-1b49-44a5-9e7e-c390efcd3a7b.png align="center")

Here in the query editor, we select the data source as the glue data catalog we just created. Then we can select spacex\_db in databases. This will then show the list of all the tables and views available inside spacex\_db. We can now write queries in the right-hand pane of the query editor. Below I have shown a simple example where I have written a select query to query the data stored in one of the tables. Once we click on Run, we can view the results of the query below, along with the time it takes to execute the query and the total data scanned by the query.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311296563/1232eacf-a679-4351-aad4-028b8e9a8ad0.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311314288/461a4029-8ffd-4b4c-9273-4ce0ed0b1e80.png align="center")

Then in this Athena query editor, we can perform different queries for data analysis, here are some of the examples.

1. Select all data from the spacex\_crew table
    

```jsx
SELECT * from spacex_crew;
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311367737/43250710-368a-4c48-bbc9-0df8699feb03.png align="center")

2. Select specific columns from the spacex\_crew table
    

```jsx
SELECT id as member_id,
name as member_name,
status,
agency
from spacex_crew
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311410714/9f124867-7200-4e9f-b289-51b63959883e.png align="center")

3. Finding the count of total crew members using the COUNT() aggregation function
    

```jsx
SELECT count(*) as total_crew_count 
from spacex_crew;
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311419480/99df8653-dbc2-432a-8f50-5f94a40094b1.png align="center")

4. Filtering data to get details of crew members belonging to “SpaceX” agency
    

```jsx
SELECT * from spacex_crew
where agency = '"SpaceX"';
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311436730/cb6229c7-0c0b-4ff1-a818-061447959823.png align="center")

5. Aggregating the data based on agency to find the number of members belonging to each agency.
    

```jsx
SELECT agency, count(id) as crew_count 
from spacex_crew
group by agency
order by crew_count desc;
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710311471173/c918e218-3b73-4589-87df-9578239c2fd9.png align="center")

Hence in this way, we can run different SQL queries to analyze the data from the data lake in AWS Athena. Here I have only shown a few examples of queries, you can try to run more complex queries using joins, subqueries, etc. based on your data model. Also, if we want to do more complex data transformations, cleaning, and analysis in our data we will use spark notebooks from Glue ETL to process the data.

## Conclusion

In conclusion, the combination of AWS Glue and AWS Athena offers a powerful solution for data analytics on the AWS platform. By utilizing AWS Glue's data crawler to ingest data from the s3 data lake, creating a Glue data catalog with databases and tables, and analyzing the data in Glue tables using SQL queries in AWS Athena, valuable insights can be effectively extracted the data, as we have done in our project so far. These tools streamline the process of data discovery, preparation, and integration, making it easier for businesses to gain a competitive edge by making data-driven decisions, improving operational efficiency, and identifying new business opportunities in today's data-driven world.

## Epilogue

Hence, simple data analytics was done over the data we loaded in the S3 data lake using AWS Glue and AWS Athena. In the next part of this project, I will be cleaning and transforming the data using spark notebooks in AWS Glue and will finally load the transformed data into AWS Redshift, which will be the final data warehouse for storing the processed data.

Previous Blog Link: [Unlock Big Data Insights: A Beginner's Guide to ELT with Airbyte & S3 (](https://anish-shilpakar.com.np/unleashing-the-power-of-elt-in-aws-using-airbyte)[anish-shilpakar.com.np](http://anish-shilpakar.com.np)[)](https://anish-shilpakar.com.np/unleashing-the-power-of-elt-in-aws-using-airbyte)

## [References](https://anish-shilpakar.com.np/unleashing-the-power-of-elt-in-aws-using-airbyte)

1. [https://docs.aws.amazon.com/glue/latest/dg/what-is-glue.html](https://anish-shilpakar.com.np/unleashing-the-power-of-elt-in-aws-using-airbyte)
    
2. [https://docs.aw](https://anish-shilpakar.com.np/unleashing-the-power-of-elt-in-aws-using-airbyte)[s.amazon.com/athena/latest/ug/what-is.html](https://docs.aws.amazon.com/athena/latest/ug/what-is.html)