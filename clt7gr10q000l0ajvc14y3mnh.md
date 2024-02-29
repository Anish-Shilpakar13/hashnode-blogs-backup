---
title: "Unleashing the power of ELT in AWS using Airbyte"
seoTitle: "Unlock Big Data Insights: A Beginner's Guide to ELT with Airbyte & S3 "
seoDescription: "This comprehensive guide explores the ELT approach for data processing in AWS, utilizing Amazon S3 as your data lake. We also introduce Airbyte, an open-sou"
datePublished: Thu Feb 29 2024 16:52:11 GMT+0000 (Coordinated Universal Time)
cuid: clt7gr10q000l0ajvc14y3mnh
slug: unleashing-the-power-of-elt-in-aws-using-airbyte
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1709225397047/6e9a8f1f-5b72-441f-b8e8-0c8f509d96cd.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1709225471003/8d486226-470f-4a70-9b69-cd7b8da5d851.png
tags: data, aws, s3, data-engineering, elt, airbyte

---

# Introduction

At present, data exists like a vast ocean, and extracting valuable insights from this is like looking for a small needle in a big haystack. Enter ELT(Extract, Load, Transform) the modern way to process and analyze data, and data lakes, a storage capable of storing large amounts of big data of different types. Extract, Load, and Transform (ELT) is a data processing approach widely used in data warehousing and analytics. It involves extracting data from various sources, loading it into a target data store such as a data lake, and then performing transformations like data cleaning, aggregation, and processing within the target storage system. Unlike traditional ETL, where data is transformed before being stored, ELT is a modern approach used in big data fields and is closely related to concepts like data lakes and lake houses. ELT offers greater flexibility and scalability in handling large volumes of data, making it ideal for processing data such as website logs and customer reviews. While there are numerous tools available for ELT in the big data field, Airbyte has emerged as a popular choice due to its simplicity, versatility, ease of use, and open-source nature. Similarly, a data lake is like having a central data source for storing raw data regardless of its format and source, where data can come from various places like sensors, social media, or online transactions. AWS offers Amazon S3, object storage offering virtually unlimited storage and capable of storing any kind of files so it is suitable for use as a data lake solution in AWS platform.

In this article, I have explored the use of ELT in AWS, using AWS S3 as a data lake. This blog serves as a tutorial on how to connect Airbyte with an S3 data lake and perform ELT operations from a REST API to an S3 data lake using Airbyte.

## Architecture Overview

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139613677/93259586-368e-4b58-b69c-0ef2478ee517.png align="center")

In the architecture above, I've illustrated a basic ELT pipeline with a REST API as the data source. In this example, I've utilized the SpaceXAPI, which offers SpaceX-related data and is accessible in Airbyte as a source connector. Airbyte serves as the ELT tool, ingesting data from the REST API and storing it in an AWS S3 bucket. This S3 bucket functions as a data lake.

## About Airbyte, The ELT tool

Airbyte is an open-source ELT platform that enables the creation of efficient data pipelines for data ingestion and replication from various sources to different destinations. It utilizes connectors to pull data from specific sources and push the ingested data to the required destination. Being open-source, engineers worldwide contribute to this project, creating and maintaining several connectors. These contributions have positioned Airbyte as a top tool for data integration, replication, migration, and ELT. Airbyte offers a no-code, cost-effective solution for building ELT pipelines. It also facilitates users to configure the frequency of data synchronization, select the data to be replicated, and determine how the data is written to the destination

## About Amazon S3, The Data Lake

Amazon S3 (Simple Storage Service) is a highly scalable, secure, and cost-effective object storage service designed to store data in the form of objects. An object consists of the file itself and any metadata describing the file, while a bucket acts as a container for these objects. S3 can accommodate virtually any amount of data, from gigabytes to petabytes and beyond, and can effortlessly scale up as needed. This scalability makes it a cost-effective data storage solution in the big data world, where data volume is rapidly increasing. S3 is versatile and can store various types of data files, including structured CSV files, semi-structured JSON and XML files, and even unstructured text, images, and videos. As part of AWS, S3 follows a pay-as-you-go model, making it cost-effective, scalable, highly available, and fault-tolerant. It also provides robust security, compliance, and support features. These factors have contributed to the widespread use of Amazon S3 and other object stores, such as Minio, which follows the S3 protocol, as data lakes in the big data field.

# Implementation Details

## Configuring the AWS S3 Data Lake

Firstly, we will need to sign in to the AWS management console with the AWS account and then follow these steps.

1. Go to the AWS management console.
    
2. Search for S3.
    
3. Click on 'Create a new bucket,' select a region of your choice, and give the data bucket a unique name. We can use a format like &lt;your\_name&gt;-s3-datalake. For example, in my case, it is 'anishshilpakar-s3-datalake.' We can leave all other default settings, such as versioning and encryption. Then, click on 'Create bucket' to create this new bucket.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139639939/bebefd70-f625-43b2-918c-741610880700.png align="center")

Once the bucket was created, I created a folder named "airbyte" to store data from the ELT operation in Airbyte. Inside the "airbyte" folder, I created another folder called "spacex" to store the data ingested from the SpaceX API.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139654749/bff6f13e-6477-4fae-9efb-4bd98f3bd317.png align="center")

## Configuring Airbyte

In Airbyte, we will configure the source connector, destination connector, and the connection between the source and destination.

### 1\. Configuring the source connector for SpaceX API

Airbyte offers a SpaceX API connector, which can be found in the "Sources" section of Airbyte. This API provides various data related to SpaceX, and I am using it for demonstration in this project. I configured the source connector in the following steps:

a. Once we are in the sources section of airbyte, we click on the new source icon

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139670276/52073016-78b0-4275-8ba2-2795e408df74.png align="center")

This will take us to a page where we can view all the available source connectors for different kinds of sources. Here, we will search for the SpaceX API.

b. Search for SpaceX API as shown below:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139691691/1f282f1c-ee2b-44c7-8740-1be20b675d3f.png align="center")

c. Once we select the SpaceX API, we will be redirected to the setup page for the source. Here, we don’t need to configure any fields, as the SpaceX API is a free API included in Airbyte itself. Simply click on the "Setup Source" button, and this will set up the required source connector for the SpaceX API. Additionally, in the right-hand section, we can find a setup guide that provides details for configuring the SpaceX API with our API key, which is an optional step performed to enhance the security of the connection.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139703723/3d43915f-60c2-489d-af84-1c4af700aa1c.png align="center")

Once we click "Setup Source," Airbyte will start testing the connection. Once the tests are completed, it will show us an output stating that all the connection tests have been passed, as shown below.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139713863/52e8214a-7c0f-498b-a288-4c640daf0501.png align="center")

Here, since we are using a built-in API available in Airbyte, errors are not expected when performing tests. However, if you use other APIs or sources with different settings, you may encounter errors if the connection settings are not correct.

### 2\. Configuring the destination connector for AWS S3.

Once the source is configured, we will now configure the destination connector, which will connect Airbyte with our AWS S3 bucket, the destination data lake. The following steps are performed:

a. Navigate to the "Destinations" section in the Airbyte console and then click on the "New Destination" button.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139723285/d7e278cb-7625-42f8-99c6-95e9008cd004.png align="center")

b. Once we click on the "New Destination" button, we will be shown a list of different destination connectors. Here, we will search for S3 and select it.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139738232/e3095da9-f92e-40b3-970e-73e35d87aabb.png align="center")

c. On the next page, we need to configure S3 by providing connection details such as AWS Access Key ID, AWS Secret Access Key, Bucket Name, Bucket Path, Bucket Region, and Flattening and Compression options.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139751773/29269c5b-a628-4ef4-b6f2-e5b282d403a0.png align="center")

1. Firstly provide a meaningful name for the destination connector
    
2. Then we need to provide the S3 key ID and S3 Access Key for the IAM user/role in the AWS account. This is used for authentication in AWS and will grant airbyte permission to upload the ingested data in your s3 bucket.
    
3. Thirdly, provide the name of the s3 bucket that was created for the data lake.
    
4. Then provide a bucket path up to your spacex folder.
    
5. Then we need to provide the region where s3 bucket is hosted.
    
6. In the output format, choose an output format between CSV, JSON, Avro, and Parquet. This determines how the data is stored in the destination.
    
7. Select a compression option. Choosing GZIP compression stores the data as compressed .gz files in your S3 bucket, reducing file size and aiding in faster data uploads.
    
8. Choose between storing data without any flattening or performing root-level flattening. No flattening stores all the data from the source inside a column called "airbyte\_data" in JSON format. Root-level flattening processes the data from the source at the root level, resulting in better-processed data.
    

Additionally, there are optional fields where we can specify the S3 endpoint, S3 path format for organizing the data file inside the S3 bucket directory, and S3 filename format for the files stored in the S3 bucket. The endpoint option should be used if we use other object stores that use the S3 protocol, such as Minio object storage, as a data lake for the destination. The setup guide can be found on the right side, which guides us to configure the S3 destination connector.

Once all the settings are configured, click on "Setup Destination." Airbyte will then start some tests on the connection, and if all the connection options are correct, we will see an output indicating that the connection looks good as shown below:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139771239/307de777-2734-4cdc-a7be-2d3ba29be2ea.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139777052/9518c2db-26ad-4ebb-a8c6-23fa4d2e7d3f.png align="center")

### 3\. Configuring the ELT connection in airbyte between source and destination.

Once, the source connector and destination connector are correctly configured and all the tests are passed, we can configure the ELT connection between source and destination.

For this, the following steps are performed.

a.Navigate to the "Connections" page in the Airbyte console and then click on "New Connection.”

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139794245/ea256dcb-8de2-494e-b283-ac80cea6f9c2.png align="center")

b. In the "Define Source" page, click on the option "Select an existing source" and then select the source connector for our SpaceX API.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139810322/2f2f9380-750a-4787-9e63-834255177693.png align="center")

c. In the "Define Destination" page, click on the option "Select an existing destination" and then select the destination connector for our AWS S3 data lake.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139822034/c3aba781-0ce1-435c-ae24-3222ef247279.png align="center")

This will now display a page that shows fetching schema as shown below.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139831836/ca4d7ce5-9367-48af-8e3b-0e4255effbe0.png align="center")

d. Once the schema has been fetched, We are redirected to a page where we can configure the different settings for your connection. These settings include the connection name, schedule type, replication frequency, destination namespace, destination stream prefix, mode to detect changes, and the options to activate streams that we want to sync and how we want to sync those streams.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139845413/61102ded-5ec9-4793-a0e3-844d357929d6.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139871337/d7016fec-c90f-471c-b465-0279a0e0bfbb.png align="center")

1. Firstly, we should provide a meaningful name for the ELT connection
    
2. We can choose where our data will reside by choosing between locations like US and EU.
    
3. Airbyte offers three types of sync schedules: scheduled sync, manual sync, and automated sync using cron jobs. For now, we are using a scheduled sync to automate the running of this ELT pipeline as per the specified schedule.
    
4. In the replication frequency, we specify at which intervals we want to perform sync between source and destination.
    
5. The destination namespace defines where the data will be synced in the destination folder. Here we can configure folder structure for synced data files and how to name the folders for synced data.
    
6. The destination stream prefix can be used to add a prefix before every data folder is synced and is an optional field.
    
7. In the next option, we can choose whether to automatically propagate field changes in the source or to propagate both field and schema changes in the source or to manually approve changes.
    
8. In the next step, we need to activate the streams from the source that we want to replicate/ingest in our destination, so folders are only created for the streams that are activated here inside the destination folder.
    
9. Also for each activated stream, we have to select a sync mode. In the sync mode, we have options for full refresh and incremental load in the source side and whether to overwrite all data or append new files on the destination side.
    

Once all the settings are properly configured, we click on "Setup Connection" and can then move on to run the ELT connection and perform data ingestion from our REST API to the S3 data lake.

## Running ELT pipeline in Airbyte

When we perform a scheduled sync, the sync is started once we click on "Setup Connection," and we can see the progress of data sync on the status page.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139892561/4498d0a7-ffc3-4cb3-9707-37c54c339fc9.png align="center")

To get more details about the data sync, we can navigate to the Job History page, where we can see the currently running jobs and also check and download logs for that running job.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139907296/c52c90fa-164b-4ea2-8aac-3cf7a9a114f3.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139915984/44fc817b-7005-4204-b5c0-9b66ffc976b5.png align="center")

Once the ELT connection has been established, we can still reconfigure the source streams to be synced and the sync mode for the streams by navigating to the Replication section.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139934021/3f4b5fa8-189d-4697-ba66-281357bc883d.png align="center")

Similarly, the Transformation section provides options to perform post-sync transformations on the ingested data. However, at the moment, post-sync transformations are limited to specific destinations like RedShift, Postgres, Oracle, MySQL, MSSQL, Snowflake, and BigQuery, and are not available for S3 buckets.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139945291/5c94e154-b7a8-43e2-8fe6-620c073385df.png align="center")

In the Settings section, we can configure the name, schema update notifications, and data residency, and have the option to delete the connection.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139956367/80b9ee74-35c3-40f5-a2d0-f4309261bbfb.png align="center")

Once the data sync has been completed, we can see the output as shown below in the Status section and Job History section

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139968807/b5051ec2-7830-48cf-8818-3de1c3b78884.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139976649/b39b94eb-3771-4e64-9efe-d5ddd6e16371.png align="center")

Now we can verify whether the data has been correctly replicated or not by navigating to the AWS S3 Data lake bucket.

## Verifying Results in AWS Data Lake

Now if we navigate to our s3 bucket in the AWS management console, we can see the data being ingested successfully as shown below

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709139997192/65f6159a-5668-4c09-80b3-f61e81f4e448.png align="center")

Different folders have been created for the different data streams ingested using Airbyte.

If we enter these folders, we can see CSV files containing the actual data ingested from the API in our ELT process in Airbyte.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709140007311/9affdaf9-4201-462b-8a67-480c76e2435c.png align="center")

We can now preview this data by querying using s3 select

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709140017027/87a9e345-51b2-417d-a21e-bf2641a4879b.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709140023660/88016dba-002c-4c08-9937-5e75ed1709f0.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709140032325/3a4e1d8d-20e3-4b23-b052-8fc254139405.png align="center")

# Conclusion

In conclusion, the ELT (Extract, Load, Transform) process, coupled with a data lake storage solution like Amazon S3, provides a powerful and scalable way to process and analyze large volumes of data. Airbyte, an open-source ELT platform, offers a user-friendly interface to configure data pipelines and automate data ingestion from various sources to destinations like S3. The combination of Airbyte and S3 allows for efficient data replication, transformation, and storage, making it ideal for handling diverse data types and sources. With the detailed steps outlined in this article, setting up an ELT pipeline with Airbyte and AWS S3 becomes straightforward, enabling organizations to leverage their data effectively for insights and decision-making.

## Epilogue

Hence, the ELT process was completed using Airbyte, and the data was ingested from a REST API into our Amazon S3 data lake. In the second part of this project, I will read this ingested data in AWS Glue and perform EDA and some transformations on the ingested data using Apache Spark in AWS Glue. Finally, I will load the transformed processed data into AWS Redshift, which will be our final data warehouse for storing the processed data.

## References

1. [Core Concepts | Airbyte Documentation](https://docs.airbyte.com/using-airbyte/core-concepts/)
    
2. [What is Amazon S3? - Amazon Simple Storage Service](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)