---
title: "Real-Time Data Engineering: Streaming Unstructured Data to AWS with Apache Spark"
datePublished: Tue Sep 17 2024 18:15:00 GMT+0000 (Coordinated Universal Time)
cuid: cm8agmmhf000509jg3ga8duqn
slug: real-time-data-engineering-streaming-unstructured-data-to-aws-with-apache-spark
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1742055995817/25c46615-92b2-4643-bbb7-bec48b5a9b8a.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1742058383424/5fcb6d2a-fedc-4a2a-8f8a-0ed0f7f14d4e.png
tags: data, aws, spark, data-engineering, aws-glue, aws-s3

---

## Introduction

In the era of big data, organizations generate vast amounts of unstructured data in formats such as JSON, CSV, and TXT files. Processing and analyzing this data efficiently is crucial for real-time decision-making. This project demonstrates an end-to-end data streaming pipeline using **Apache Spark Streaming, Docker, and AWS services** to handle unstructured data seamlessly. The pipeline ingests **JSON, CSV, and TXT files** via **Spark Streaming**, processes them in a **distributed Spark cluster**, and stores them in an **S3 Data Lake**. An **AWS Glue Crawler** extracts metadata and updates the **Glue Data Catalog**, enabling **Athena** to query the data efficiently. The goal is to demonstrate how **data engineers can automate real-time processing of unstructured data** and seamlessly integrate it with AWS services for storage, metadata management, and analytics.

## Architecture

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056080931/88ab24bc-2946-4472-b3b8-a26093d1bc75.png align="center")

## About Unstructured Data

**Unstructured data** refers to information that **does not have a predefined schema or organized format**, making it difficult to store, process, and analyze using traditional relational databases. Unlike structured data, which is neatly arranged in rows and columns (e.g., SQL databases), unstructured data lacks a consistent structure and can exist in various formats such as **text, images, videos, audio, and logs**. With the rapid growth of data-driven applications, organizations generate **massive volumes of unstructured data** from **social media, IoT sensors, emails, customer reviews, and machine logs**. Data engineers play a crucial role in **extracting valuable insights** from this data by designing scalable pipelines that ingest, process, and analyze it efficiently. **Big data technologies** such as **Apache Spark, Hadoop, and AWS services** help process unstructured data and transform it into a usable format for analytics and decision-making.

Unstructured data accounts for nearly **80-90% of the world’s data** and continues to grow exponentially. Some real-world examples include:

* **Text Data**: Emails, chat messages, customer feedback, PDFs, and reports.
    
* **Multimedia**: Images, videos, and audio recordings from platforms like YouTube or surveillance systems.
    
* **Logs and Machine Data**: Server logs, application logs, IoT sensor data, and cybersecurity logs.
    
* **Social Media Content**: Tweets, Facebook posts, Instagram captions, and TikTok videos.
    

Here in this project, I am using unstructured data sources as source which include text files, and some semistructured data including CSV and JSON files. The examples of files in the source are shown below:

1. TXT file
    
    ```plaintext
    Pr111
    Product: Parle-G Biscuit
    
    Category: Food
    
    Costs Rs.20
    
    Best From 2024-05-01 to 2025-05-01
    ```
    
    About 100 such files are generated in the source by using a simple Python script as shown below:
    
    ```python
    import random
    import datetime
    
    def generate_random_product(index):
        categories = {
            "Food": [
                "Parle-G Biscuit", "Dairy Milk Chocolate", "Lay's Chips", "Oreo Cookies", "Maggie Noodles", 
                "Kellogg's Corn Flakes", "KitKat", "Bournvita", "Tropicana Juice", "Pepsi"
            ],
            "Personal Care": [
                "Colgate Toothpaste", "Dove Shampoo", "Lifebuoy Soap", "Nivea Cream", "Vaseline Lotion", 
                "Garnier Face Wash", "Old Spice Deodorant", "Himalaya Face Pack", "Gillette Razor", "Head & Shoulders Shampoo"
            ],
            "Footwear": [
                "Nike Running Shoes", "Adidas Sneakers", "Puma Sandals", "Reebok Sports Shoes", "Skechers Loafers", 
                "Woodland Boots", "Bata Formal Shoes", "Crocs Slippers", "Fila Trainers", "New Balance Sneakers"
            ],
            "Electronics": [
                "Samsung Galaxy S21", "Apple iPhone 14", "Sony Headphones", "JBL Bluetooth Speaker", "Dell Laptop", 
                "HP Printer", "LG Smart TV", "Canon DSLR Camera", "Fitbit Smartwatch", "Logitech Keyboard"
            ]
        }
        
        category = random.choice(list(categories.keys()))
        product = random.choice(categories[category])
        product_code = f"Pr{1000 + index}"
        cost = random.randint(1000, 10000)  # Random cost between Rs.10 and Rs.500
        
        start_date = datetime.date.today() + datetime.timedelta(days=random.randint(0, 365))
        end_date = start_date + datetime.timedelta(days=365)
        
        content = f"""
    {product_code}
    Product: {product}
    
    Category: {category}
    
    Costs Rs.{cost}
    
    Best From {start_date} to {end_date}
    """.strip()
        
        return content
    
    def save_to_file(content, filename):
        with open(filename, "w") as file:
            file.write(content)
        print(f"File '{filename}' has been created with random product details.")
    
    if __name__ == "__main__":
        for i in range(1, 101):  # Generate 100 files with unique product codes
            product_details = generate_random_product(i)
            file_path = f"./source_files/txt_source/product_details_{i}.txt"
            save_to_file(product_details, file_path)
    ```
    

2. CSV file
    
    ```python
    p_id,p_name,p_category,p_price,p_created_date,p_expiry_date
    Pr1,Power Motors,Electronics,2500,2025-01-01,2025-09-30 
    ```
    
3. JSON file
    
    ```python
    {
        "p_id": "Pr11",
        "p_name": "Gaming Mouse",
        "p_category": "Computer",
        "p_price": 1000,
        "p_created_date": "2023-09-10",
        "p_expiry_date": "2026-05-10"
    }
    ```
    

## About Apache Spark and Spark Streaming

Apache Spark is an **open-source, distributed computing framework** designed for fast and scalable big data processing. Unlike traditional batch processing systems, Spark performs computations in memory, significantly improving performance. It follows a **Master-Slave architecture**, where a **Spark Master** manages the cluster, and multiple **Spark Workers (Executors)** execute tasks in parallel. Spark Streaming is a **real-time data processing engine** that enables micro-batch processing of streaming data. It can ingest data from **various sources like Kafka, Flume, AWS S3, and file systems**, process it using Spark’s **DAG (Directed Acyclic Graph)** execution model, and output results to databases, dashboards, or cloud storage making it ideal for log analysis, fraud detection and IOT applications

To simplify Spark deployment, **Docker** is used to containerize Spark components, ensuring **portability, scalability, and ease of configuration**. Using **Docker Compose**, we define Spark’s cluster setup, including **one master node and three worker nodes**, making the environment easily replicable across different systems. The docker-compose file for spark configuration is shown below:

```yaml
version: '3.8'

x-spark-common: &spark-common
  image: bitnami/spark:3.5.1
  volumes:
    - ./jobs:/opt/bitnami/spark/jobs
  command: bin/spark-class org.apache.spark.deploy.worker.Worker spark://spark-master:7077
  depends_on:
    - spark-master
  environment:
    SPARK_MODE: worker
    SPARK_WORKER_CORES: 1
    SPARK_WORKER_MEMORY: 1g
    SPARK_MASTER_URL: spark://spark-master:7077
  networks:
    - spark-network
  restart: always

services:
  spark-master:
    image: bitnami/spark:3.5.1
    volumes:
      - ./jobs:/opt/bitnami/spark/jobs
    command: bin/spark-class org.apache.spark.deploy.master.Master
    ports:
      - "9090:8080"
      - "7077:7077"
    networks:
      - spark-network
    restart: always
  
  spark-worker-1:
    <<: *spark-common
  
  spark-worker-2:
    <<: *spark-common

  spark-worker-3:
    <<: *spark-common
  
networks:
  spark-network:
    driver: bridge
```

The provided **Docker Compose** file configures Spark with:

* **Spark Master**: Runs on port **7077** and manages the cluster.
    
* **Three Spark Workers**: Each with **1 CPU core and 1GB memory** (`SPARK_WORKER_CORES: 1`, `SPARK_WORKER_MEMORY: 1g`).
    
* **Volumes**: Mounted to share Spark jobs (`./jobs:/opt/bitnami/spark/jobs`). This jobs folder includes the actual spark job which will read the unstructured data from source files, parse them, and store them in the AWS S3 data lake.
    
* **Network Bridge**: `spark-network` enables inter-coAntainer communication.
    
* **Auto Restart**: Ensures the services restart automatically in case of failure.
    

## About AWS Services Involved

### Amazon S3 as a Data Lake for Streaming Unstructured Data\*\*

Amazon **Simple Storage Service (S3)** is a highly scalable and durable **object storage** service that serves as the foundation for building **data lakes** in AWS. A **data lake** is a centralized repository that allows storing **structured, semi-structured, and unstructured data** at any scale. In this project, **Spark Streaming reads unstructured files (CSV, JSON, TXT), processes them into structured data using Spark DataFrames, and writes the output as Parquet files to S3**, making it ready for analytics.

For configuring S3 as a data lake, firstly new access keys were set up using AWS IAM. The following were the steps taken

1. Firstly navigate to AWS Console and then search for IAM
    
2. Then select Users &gt; user\_name &gt; Create Access Key. You will be redirected to the screen as shown below
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056377436/9c360275-ae79-4556-872a-d4429f7e0988.png align="center")
    
    Select the command line interface and tick on confirmation, this will then create new access keys and you will be redirected to a new screen as shown below
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056419595/c577127a-2c3b-4853-8a5f-b5f3b6df8309.png align="center")
    
    Here you can copy the access key and secret access key and store them in a safe location as these keys will later be used for accessing S3 buckets for storing the parsed data. Alternatively, the keys can also be downloaded as a CSV file.
    
    The next step is creating a new S3 bucket which will be used to store the parsed data.
    
    1. For this search for AWS S3 and navigate to AWS S3. Click on Create New Bucket.
        
    2. Here configure the bucket as shown below, choose an appropriate name that should be globally unique.
        
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056505331/6795100e-a7a7-461d-83a1-5c2c471bc5d0.png align="center")
    
    Initially, the S3 bucket will be empty, but once we run the spark job, this bucket will be populated by folders like checkpoints and data which is dynamically created by the spark job.
    

### AWS Glue

AWS **Glue** is a fully managed **ETL (Extract, Transform, Load) service** that enables seamless **data discovery, cataloging, and transformation** in the AWS ecosystem. In this project, Glue plays a critical role in making the unstructured data **queryable and analyzable** after being processed by Spark Streaming and stored in **Amazon S3 as Parquet files**.

#### **Key Components of AWS Glue in This Project**

1. **Glue Crawler**
    
    * A Glue **Crawler scans the S3 bucket** detects new Parquet files, and **extracts metadata** such as column names, data types, and table structure.
        
    * It **automatically updates the Glue Data Catalog**, ensuring that any new or updated data is discoverable.
        
    * The crawler runs periodically to detect **new files added to S3** as part of the Spark Streaming pipeline.
        
2. **Glue Data Catalog**
    
    * Acts as a **centralized metadata repository**, storing schema details for all discovered datasets.
        
    * Enables seamless integration with **AWS Athena, Redshift, and other AWS analytics services**.
        
    * Provides a **unified view of structured data** extracted from unstructured files stored in S3.
        
3. **Glue Database and Tables**
    
    * The Glue Crawler **creates a new table** inside a designated **Glue Database** based on the schema detected in the S3 data lake.
        
    * Each time new files arrive in S3, the table is **automatically updated** to reflect the changes.
        
    * The Glue database acts as a **logical grouping** for related datasets, making it easy to manage and query structured data.
        
    * Here the crawler will create a table named bronze.
        

To configure AWS Glue, the following steps are taken

1. Firstly search for AWS Glue in the IAM console and navigate to Glue
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056592289/c8f1e2be-7971-4fac-944c-2b6fb5c898ef.png align="center")
    
2. Then navigate to the Crawlers page to create a new glue crawler
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056610489/df08d7fd-09b1-4ee9-a28a-60d92b2b41df.png align="center")
    
3. Configure a new crawler as shown below.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056642285/07f4e243-ee58-490d-acf8-0c25099c26a7.png align="center")
    
    Here we need to add the S3 bucket path of the source folder as the data source as shown below.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056677340/a224b664-d352-448d-8037-0c9f02c59d5c.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056702980/2e477c92-b9a9-488f-a8a4-9c91172494fa.png align="center")
    
    Also, we need to create a new IAM role to allow the crawler to create a new table.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056726171/51a1e921-9764-4947-accd-becbc9289f63.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056742254/8dfd6415-80f3-4138-ad15-77bc3c931a1c.png align="center")
    
    After creating IAM role, we also have to create new glue database to store the tables as shown below.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056765095/9221803d-2e09-474e-8acd-c4a07c04d993.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056785260/6cef788a-4fed-4395-96a1-db1b0923c926.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056808344/5d97c395-6b0d-478d-8d85-897ab3818638.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056830700/9b0669a6-1e91-44bf-b084-e106ebf4415a.png align="center")
    
    Once the crawler is created, we can run the crawler to crawl the data from the S3 data lake and to create a new table in the AWS Glue database.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056868224/f10a4921-bcab-4496-9af8-d90c81e6fb7f.png align="center")
    

### AWS Athena

AWS **Athena** is a **serverless, interactive query service** that allows users to analyze structured data stored in **Amazon S3** using standard **SQL**. In this project, Athena plays a crucial role in **querying the processed unstructured data** after it has been **streamed, structured, and cataloged** by Apache Spark and AWS Glue.

After the Glue crawler creates a new table, AWS Athena can be used to query this newly created table. But before querying we need to do some configuration in AWS Athena.

For this navigate to AWS Athena by searching it in AWS Management console. Here we need to configure query result location where we configure S3 location to store query results.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056914016/2989e8c7-32f2-4452-8c7d-175f6664cda0.png align="center")

## End-to-End Workflow Explanation

The end-to-end workflow for this project follows these steps:

1. **Uploading Source Files**
    
    * Firstly, Input files (**TXT, CSV, JSON**) are uploaded into designated S3 source folders.
        
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742056955095/3fefd6b1-5c81-45e0-ad30-fdb783ccc8e3.png align="center")
    

2. **Spark Job Processing**
    
    * Spark reads and processes these files using appropriate methods:
        
        * **CSV & JSON:** Directly loaded into Spark DataFrames.
            
        * **TXT Files:** Processed using **regular expressions** and **user-defined functions (UDFs)** to extract required fields.
            
    * For example, UDFs extract details like `product_id`, `product_name`, `category`, `price`, and dates.
        
    
    **Spark Job Code**
    
    ```python
    from pyspark.sql import SparkSession, DataFrame
    from pyspark.sql.types import StructType, StructField, StringType, DoubleType, IntegerType, DateType
    from pyspark.sql.functions import udf, regexp_replace
    from config.config import configuration
    
    from datetime import datetime
    import re
    
    def extract_p_id(file_content):
        file_content = file_content.strip()
        # First word of the file is the product id
        match = re.search(r'^(\w+)', file_content)
        p_id =  match.group(1) if match else None
        return p_id
    
    def extract_p_name(file_content):
        # search for Product: <product_name>
        match = re.search(r'Product:\s*(.+)', file_content)
        p_name = match.group(1).strip() if match else None
        return p_name
    
    def extract_p_category(file_content):
        # search for Category: <product_category>
        match = re.search(r'Category:\s*(.+)', file_content)
        p_category = match.group(1).strip() if match else None
        return p_category
    
    def extract_p_price(file_content):
        # Search for Costs Rs.<price>
        match = re.search(r'Costs\s*Rs\.(\d+)', file_content)
        price = float(match.group(1)) if match else None
        return price
    
    def extract_dates(file_content):
        match = re.search(r'Best From\s*(\d{4}-\d{2}-\d{2})\s*to\s*(\d{4}-\d{2}-\d{2})', file_content)
        if match:
            start_date = datetime.strptime(match.group(1), '%Y-%m-%d')
            end_date = datetime.strptime(match.group(2), '%Y-%m-%d')
            return start_date, end_date
        return None, None
    
    def define_udfs():
        return {
            'extract_p_id_udf': udf(extract_p_id, StringType()),
            'extract_p_name_udf': udf(extract_p_name, StringType()),
            'extract_p_category_udf': udf(extract_p_category, StringType()),
            'extract_p_price_udf': udf(extract_p_price, DoubleType()),
            'extract_dates_udf': udf(extract_dates, StructType([
                StructField('p_created_date', DateType(), True),
                StructField('p_expiry_date', DateType(), True)
            ])),
        }
    
    def streamWriter(input: DataFrame, checkpointFolder, output):
        return (
            input
            .writeStream
            .outputMode('append')
            .format('parquet')
            .option('path', output)
            .option('checkpointLocation', checkpointFolder)
            .trigger(processingTime='5 seconds')
            .start()
        )
    
    if __name__ == '__main__':
        # Initialize the Spark session
        spark = (
            SparkSession.builder.appName('Unstructured_Data_Streaming_Spark_AWS')
            .config("spark.jars.packages", 
                    'org.apache.hadoop:hadoop-aws:3.3.1,'
                    'com.amazonaws:aws-java-sdk:1.11.469')
            .config('spark.hadoop.fs.s3a.impl','org.apache.hadoop.fs.s3a.S3AFileSystem')
            .config('spark.hadoop.fs.s3a.access.key',configuration.get('AWS_ACCESS_KEY'))
            .config('spark.hadoop.fs.s3a.secret.key',configuration.get('AWS_SECRET_KEY'))
            .config('spark.hadoop.fs.s3a.aws.credentials.provider','org.apache.hadoop.fs.s3a.SimpleAWSCredentialsProvider')
            .getOrCreate()
        )
    
        # Variables for source files
        txt_src_dir = 'file:///opt/bitnami/spark/jobs/source_files/txt_source'
        csv_src_dir = 'file:///opt/bitnami/spark/jobs/source_files/csv_source'
        json_src_dir = 'file:///opt/bitnami/spark/jobs/source_files/json_source'
    
        # define data schema
        # p_id,p_name,p_category,p_price,p_create_date,p_expiry_date
        data_schema = StructType([
            StructField('p_id', StringType(), True),
            StructField('p_name', StringType(), True),
            StructField('p_category', StringType(), True),
            StructField('p_price', DoubleType(), True),
            StructField('p_created_date', DateType(), True),
            StructField('p_expiry_date', DateType(), True),
        ])
    
        # define udfs
        udfs = define_udfs()
    
        # reading from a file stream
        txt_file_df = (
            spark.readStream
            .format('text')
            .option('wholetext','true')
            .load(txt_src_dir)
        )
    
        txt_file_df = txt_file_df.withColumn('p_id', regexp_replace(udfs['extract_p_id_udf']('value'), r'\r',' '))
        txt_file_df = txt_file_df.withColumn('p_name', udfs['extract_p_name_udf']('value'))
        txt_file_df = txt_file_df.withColumn('p_category', udfs['extract_p_category_udf']('value'))
        txt_file_df = txt_file_df.withColumn('p_price', udfs['extract_p_price_udf']('value'))
        txt_file_df = txt_file_df.withColumn('p_create_date', udfs['extract_dates_udf']('value').getField('p_created_date'))
        txt_file_df = txt_file_df.withColumn('p_expiry_date', udfs['extract_dates_udf']('value').getField('p_expiry_date'))
    
        txt_parsed_df = txt_file_df.select('p_id', 'p_name', 'p_category', 'p_price', 'p_create_date', 'p_expiry_date')
    
        # reading from json source
        json_df = (
            spark.readStream
            .json(json_src_dir,schema=data_schema,multiLine=True)
        )
    
        # reading from csv source
        csv_df = (
            spark.readStream
            .format('csv')
            .option('header', 'true')
            .schema(data_schema)
            .load(csv_src_dir)
        )
    
        # union all the dataframes
        src_union_df = txt_parsed_df.union(json_df).union(csv_df)
    
        # to write the dataframe into s3 data lake in parquet format
        query = streamWriter(src_union_df, checkpointFolder='s3a://anish-shilpakar-bucket/checkpoints', output='s3a://anish-shilpakar-bucket/data/spark_unstructured/bronze')
    
        #to display the read stream in console as output
        # query = (
        #     src_union_df
        #     .writeStream
        #     .outputMode('append')
        #     .format('console')
        #     .option('truncate', False)
        #     .start()
        # ) 
    
        query.awaitTermination()
    
        spark.stop()
    ```
    
    This Spark application is designed to **process unstructured text, CSV, and JSON files**, extract meaningful information, and store the structured data in **AWS S3 as Parquet files**. **Code Explanation**
    
    1. **Importing Required Libraries:**
        
        * Uses `pyspark.sql` for Spark DataFrames, UDFs, and schema definitions.
            
        * Imports `re` and `datetime` for text processing and date parsing.
            
    2. **Text Processing Functions:**
        
        * Defines **UDFs (User-Defined Functions)** using **Regular Expressions (RegEx)** to extract:
            
            * `p_id`: Extract the product ID (first word).
                
            * `p_name`: Extract the product name (after "Product:").
                
            * `p_category`: Extract the product category (after "Category:").
                
            * `p_price`: Extract the product price (after "Costs Rs.").
                
            * `p_created_date` & `p_expiry_date`: Extract validity dates (after "Best From").
                
    3. **Spark Streaming Initialization:**
        
        * Creates a **SparkSession** with AWS S3 configurations for accessing the data lake.
            
        * Uses credentials from a **config file** (`configuration.get('AWS_ACCESS_KEY')`).
            
        * The [configuration.py](http://configuration.py) stores required access keys for S3 buckets as shown below
            
            ```python
            configuration = {
                'AWS_ACCESS_KEY': '...',
                'AWS_SECRET_KEY': '...'
            }
            ```
            
    4. **Reading Input Files (TXT, CSV, JSON) as Streams:**
        
        * **Text Files:** Read as raw text and processed using UDFs.
            
        * **JSON & CSV:** Read using Spark's built-in schema inference.
            
    5. **Data Transformation:**
        
        * Extracts required fields from **TXT files** using **UDFs**.
            
        * Ensures data consistency by applying the same **schema** across JSON, CSV, and parsed TXT data.
            
        * Combines (unions) data from all sources into a **single data frame**.
            
    6. **Streaming Data to S3:**
        
        * Uses `writeStream` to **continuously write** the structured data into **AWS S3** in **Parquet format**.
            
        * A **checkpoint directory** is maintained in S3 for fault tolerance.
            
    7. **Execution and Monitoring:**
        
        * The query **runs continuously**, processing new files every **5 seconds** (`trigger(processingTime='5 seconds')`).
            
        * Optionally, data can be displayed in the **console** for debugging.
            
        * The spark session is stopped after execution.
            
3. **Executing the Spark Job**
    
    To run the job locally in local spark
    
    > spark-submit [main.py](http://main.py)
    
    To run the job inside a docker-based distributed spark cluster
    
    > docker exec -it docker\_setup-spark-master-1 spark-submit --master spark://spark-master:7077 jobs/[main.py](http://main.py)
    
    Once the spark job starts running, we can see that it reads the source files, creates dataframes and start streaming the results as parquet files in the s3 data lake.
    
    On inspecting logs, we can see results like shown below:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057209781/c001d2e5-84a3-4ff1-a7f1-7abed6c27307.png align="center")
    
    Once reading all files is completed and when no new files are added the logs show that the query has been idle as shown below
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057296089/9188b7b8-c0cb-4025-a621-bbf84027f795.png align="center")
    
4. **Inspecting S3 Storage**
    
    The Spark job creates two folders in S3:
    
    * **Checkpoints Folder:**
        
        * Stores metadata required for Spark Structured Streaming to track processed data and ensure fault tolerance.
            
        * Contains subfolders like:
            
            * `commits/`: Stores commit logs for processed batches.
                
            * `metadata/`: Maintains streaming query metadata.
                
            * `offsets/`: Tracks data read offsets to prevent duplicate processing.
                
            * `sources/`: Stores source-related metadata for tracking input data streams.
                
    * **Data Folder (Bronze Layer):** Stores processed Parquet files.
        
        * Contains the processed and structured data in **Parquet format** stored in an **S3 Data Lake**.
            
            * The **Bronze layer** holds raw ingested data before further transformations.
                
            * Includes `_spark_metadata/` for tracking written files and multiple `.parquet` files containing structured records.
                
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057362013/6f3957c8-4275-4767-8049-ecc750754a41.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057385410/0bf50fe5-d479-4c26-af4a-00e2e00a6d42.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057400027/01a0ff64-db7f-43ee-879e-77dcb811c761.png align="center")
    
5. **Running AWS Glue Crawler**
    
    Once the parquet files are created and stored by the spark job inside the S3 data lake, we will now run the Glue crawler which we have previously configured
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057458272/392b1d9a-964d-486b-81a3-98fd1f13fa33.png align="center")
    
    Once the Glue crawler run is completed, we can check the tables in glue database and we can see that a new table has been created.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057521376/6e34614a-99d6-43da-87aa-d312011a63a1.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057541972/581b7c7d-f1f4-4b0a-b48a-fbe564305195.png align="center")
    
6. **Querying with AWS Athena**
    
    Finally, AWS Athena can be used to query the Glue table for structured data analysis.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057578128/543ba8ca-b4be-44e1-bfd0-fbd2352319ca.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057594138/e1bb7708-eb2e-4477-b567-fb23f35da2a1.png align="center")
    
    We can see the query runs successfully and we obtain the desired result
    

**Test Real-Time Streaming**

To test whether the real-time streaming is working properly or not, we keep the spark job running by running the docker container and then adding a new file inside one of the source folders. Running spark as a docker container and checking spark UI.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057645081/981364cc-af9e-4ffc-af52-724cbf0b43b3.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057659894/dd7bf5cc-0115-4d53-9cc3-226b3dff56fd.png align="center")

Newly added CSV file with 5 new records

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057690022/4050b12c-069b-4c9e-b53e-7c213f146458.png align="center")

Now if we check the logs of the spark job we can see that it is reading the content from this new file, parsing the content, and storing the resulting parquet files inthe S3 bucket

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057710672/e59ba84d-a4e7-4a2f-b9f0-ebba4f173c2e.png align="center")

By inspecting the S3 data lake we can see the newly added file by checking the last modified column

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057734361/e8bbf2d9-b5b0-401d-a334-5aeea18bb101.png align="center")

Then we run the glue crawler to update the Glue database

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057755895/66b13383-fd10-4cf7-9ee6-55cae599cc51.png align="center")

Once the Glue crawler succeeds, we then again query the glue table like before, and this time we can see that the count has increased from 105 to 110 indicating the addition of new records from the newly added CSV file

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742057778725/aa07a0a9-925c-4ea6-830f-17016c5f8367.png align="center")

## Conclusion

This project showcases how **Apache Spark, AWS S3, Glue, and Athena** can work together to build a **real-time streaming pipeline** for processing unstructured data. We enable seamless data analysis by continuously ingesting TXT, CSV, and JSON files, transforming them into a structured format, and storing them efficiently in Parquet on S3. With **Glue crawlers automating schema detection** and **Athena providing instant querying**, this setup ensures a smooth, scalable, and efficient data processing workflow. Whether for **real-time analytics, reporting, or further transformations**, this approach lays a solid foundation for handling streaming data in the cloud. This project can now be further enhanced to transform the data in the Bronze layer, store processed data in a data warehouse like Redshift, and integrate with BI tools like AWS QuickSight or Power BI for deeper insights and visualization.

## References

1. [(3798) End to End Realtime Streaming with Unstructured Data | Get Hired as an Experienced Data Engineer - YouTube](https://www.youtube.com/watch?v=M6BWTnMH77M)
    
2. [Welcome to AWS Documentation](https://docs.aws.amazon.com/)