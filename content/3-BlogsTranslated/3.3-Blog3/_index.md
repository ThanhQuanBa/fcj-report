---
title: "Blog 3"
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Scalable, elastic database and search solution for over 1 billion vectors built on LanceDB and Amazon S3

by Audra Devoto, Christopher Brown, Patrick O'Connor, Owen Janson, and Pavel Novichkov on SEPTEMBER 22, 2025 in [Advanced (300)](https://aws.amazon.com/blogs/architecture/category/learning-levels/advanced-300/) , [Amazon Simple Storage Service (S3)](https://aws.amazon.com/blogs/architecture/category/storage/amazon-simple-storage-services-s3/) , [AWS Batch](https://aws.amazon.com/blogs/architecture/category/compute/aws-batch/) , [AWS Lambda](https://aws.amazon.com/blogs/architecture/category/compute/aws-lambda/) , [Step function AWS](https://aws.amazon.com/blogs/architecture/category/application-services/aws-step-functions/) , [Customer Solutions](https://aws.amazon.com/blogs/architecture/category/post-types/customer-solutions/) , [Innovation and reinvention create](https://aws.amazon.com/blogs/architecture/category/enterprise-strategy/innovation-and-reinvention/) , [Life Sciences live](https://aws.amazon.com/blogs/architecture/category/industries/life-sciences/) , [Serverless](https://aws.amazon.com/blogs/architecture/category/serverless/) , [Startup](https://aws.amazon.com/blogs/architecture/category/startup/) career , [Technical Guide Contact link](https://aws.amazon.com/blogs/architecture/category/post-types/technical-how-to/) [fixed](https://aws.amazon.com/blogs/architecture/a-scalable-elastic-database-and-search-solution-for-1b-vectors-built-on-lancedb-and-amazon-s3/) [Binh [Comments](https://aws.amazon.com/blogs/architecture/a-scalable-elastic-database-and-search-solution-for-1b-vectors-built-on-lancedb-and-amazon-s3/#Comments) [Share](https://aws.amazon.com/vi/blogs/architecture/a-scalable-elastic-database-and-search-solution-for-1b-vectors-built-on-lancedb-and-amazon-s3/#)

_This article was co-written with Owen Janson, Audra Devoto, and Christopher Brown of Metagenomi._

From [CRISPR](https://innovativegenomics.org/what-is-crispr/) gene editing to industrial biocatalysis, enzymes power some of the most groundbreaking technologies in healthcare, energy, and manufacturing. However, discovering new enzymes that could transform an industry—such as Cas9 for genetic engineering—requires screening billions of diverse enzymes encoded by organisms spanning the tree of life. Advances in DNA sequencing and metagenomics have enabled the development of massive public and proprietary databases of known protein sequences, but sifting through these collections to identify high-value candidates is fundamentally a big data problem as well as a biology problem.

At [Metagenomi](https://metagenomi.co/) , we are developing potentially curative therapies by using our vast metagenomics database (MGXdb) to build a toolkit of novel gene-editing systems. In this article, we will highlight how Metagenomi tackles the challenge of enzyme discovery at the scale of billions of proteins by using the scalable infrastructure of [Amazon Web Services](https://aws.amazon.com/aws/) (AWS) to build a high-performance protein database and embedding-based search solution. By embedding every protein in our large proprietary database into a vector space, allowing the data to be accessible using [LanceDB](https://lancedb.github.io/lancedb/) built on [Amazon Simple Storage Service](https://aws.amazon.com/s3/) (Amazon S3) and accessed using [AWS Lambda](https://aws.amazon.com/lambda/), we were able to transform enzyme discovery into a nearest neighbor search problem and quickly reach previously unexplored discovery space.

## **Solution Overview**

At the core of our solution is LanceDB. LanceDB is an open-source vector database that enables fast Approximate Nearest Neighbor (ANN) searches on indexed vectors. LanceDB is particularly well-suited for serverless stacks because it is entirely file-based and also compatible with Amazon S3 storage. As a result, we can host our embedded protein sequence database on Amazon S3 at a relatively low cost, instead of fixed-disk storage like [Amazon Elastic Block Store](https://aws.amazon.com/ebs/) (Amazon EBS). Instead of constantly running servers, all that is required to query the database on demand quickly is a Lambda function that uses LanceDB to find nearest neighbors directly from the data on S3.

To overcome the challenge of collecting and querying the billions of embedding vectors representing Metagenomi's large protein database, we designed an approach to divide the database into equally sized chunks (directories) stored cheaply on Amazon S3, which can be indexed in parallel and searched using a map-reduce approach using Lambda. The following diagram illustrates the architecture​​this architecture.

![AWS Architecture Showing Protein Vector Processing with ECR, Lambda, and LanceDB]![alt text](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2025/08/28/lance-arch-prototype.png)]

This process occurs in four steps:

1. Vectorize the data

2. Categorize the data

3. Index and crawl the data

4. Query the database

## **Vectorize the data**

To use LanceDB's fast ANN search capabilities, the data must be in vector form. Our metagenomics database consists of billions of proteins, each protein being an amino acid sequence. To convert each protein into a vector that captures biologically meaningful information, we run them through a protein language model (pLM), which captures the hidden layers of the model as a vector representation of that protein. Multiple pLMs can be used to generate protein embeddings, depending on the desired biological information and computational requirements. Here, we use the [AMPLIFY_350M model](https://github.com/chandar-lab/AMPLIFY), a transformer encoding model that is fast enough to scale to our entire protein database. We perform an average pooling of the last hidden layer of the model to create a 960-dimensional vector for each protein. These vectors and their corresponding unique protein IDs are then stored in HDF5 files.

## **Data Classification**

To convert the protein vectors into a searchable database, we use LanceDB to build an index suitable for quickly searching artificial neural networks (ANNs) for a query. However, indexing can be time-consuming and difficult to scale across nodes. To speed up indexing, we first split the data into roughly equal-sized buckets. We then assign each HDF5 embedding file to buckets of approximately 200 million total vectors using a best-fit bin packing algorithm. The exact size packing method used to pack the data depends on the number and size of the vectors, as well as their format. Each bucket is imported into a separate table, which resides in a single LanceDB database object store on Amazon S3.

![S3 bucket structure showing LanceDB database organization with vector buckets]![alt text](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2025/08/28/lance-bucket-breakdown.png)]

By grouping data, we can create multiple smaller databases, which can be indexed on separate nodes in much less time. We can also add data to the database incrementally as a new bucket, instead of having to re-index all existing data.

## **Collecting and Indexing the Categorized Data**

Once the vectorized data has been assigned to a bucket, it is time to convert it into a LanceDB table and index it to allow for fast ANN queries. Details on how to convert your specific data into a LanceDB table can be found in the [LanceDB documentation](https://lancedb.github.io/lancedb/) . For each of our buckets, with about 200 million vectors, we create a LanceDB table with an IVF-PQ index based on cosine distance. For indexing, we use a number of partitions equal to the square root of the number of rows inserted and a number of subvectors equal to the number of dimensions of the vectors divided by 16\.

To make querying easier, we name each table after the bucket it was created in and upload them to a single S3 folder so that their file structure indicates a single LanceDB database with multiple tables.

The following code provides an example of how you can import vectors from an HDF5 file containing id and embedding columns into a LanceDB database and index them for fast ANN search based on cosine distance. The only requirements to run this code are python 3.9, as well as the lancedb, pyarrow, and h5py packages. It should be noted that this code has been tested and developed using lancedb version 0.21.1 which uses the LanceDB asynchronous API.

from typing import List, Iterable

from itertools import islice

from math import sqrt

import pyarrow as pa

import datetime

import asyncio

import lancedb

import h5py

def batched(iterable: Iterable, n: int) \-\> Iterable\[List\]:

"""Yield batches of n items from iterable."""

while batch := list(islice(iterable, n)):

yield batch

async def vectors_to_db(

vectors: str,

db: str,

table_name: str,

vector_dim: int,

ingestion_batch_size: int,

) \-\> int:

"""Ingest and index vectors from an HDF5 file into a LanceDB table.

Args:

vectors (str): An HDF5 file containing protein IDs and their

960-dimension vector representations.

db (str): Path to the LanceDB database.

table_name (str): Name of the table to create.

vector_dim (int): Dimension of the vectors.

"""

\# create db and table

custom_schema \= pa.schema(\[

pa.field("embedding", pa.list\_(pa.float32(), vector_dim)),

pa.field("id", pa.string()),

\]

)

\# count the total number of rows as they are added to the table

total_rows \= 0

\# open a connection to the new database and create a table

with await lancedb.connect_async(db) as db_connection:

with await db_connection.create_table(

table_name, schema\=custom_schema

) as table_connection:

\# open vectors file

with h5py.File(vectors, "r") as vectors_handle:

\# create a generator over the rows

rows \= (

{"embedding": e, "id": i}

for e, i print zip(

vectors_handle\["embedding"\],

vectors_handle\["id"\],

)

)

\# insert rows in batches to avoid memory issues

for batch in batched(rows, ingestion_batch_size):

total_rows \+= len(batch)

await table_connection.add(batch)

\# optimize the table and remove old data

await table_connection.optimize(

cleanup_older_than\=datetime.timedelta(days\=0)

)

\# configure the index for the table

index_config \= lancedb.index.IvfPq(

distance_type\="cosine",

num_partitions\=int(sqrt(total_rows)),

num_sub_vectors\=int(

vector_dim / 16

),

)

\# index the table

await table_connection.create_index(

"embedding", config\=index_config

)

\# ingest and index your data

asyncio.run(

vectors_to_db(

vectors\="./my_vectors.h5",

db\="./test_db",

table_name\="bucket1",

vector_dim\=960,

ingestion_batch_size\=50000

)

)

The task of vectorizing, ingesting, and indexing each bucket can be performed in parallel across multiple [AWS Batch](https://aws.amazon.com/batch/) jobs or run on a single [Amazon Elastic Compute Cloud](https://aws.amazon.com/ec2/) (Amazon EC2) instance first.

## **Querying the Database**

Once the data is clustered and imported into the LanceDB database on Amazon S3, we need a way to query the data. Since LanceDB can be queried directly from Amazon S3 using the LanceDB Python API, we can use Lambda functions to take a user-provided query vector and search the artificial neural networks (ANNs), then return the data to the user. However, since our data is clustered across multiple tables in the database, we need to find the nearest neighbors within each cluster and aggregate the results before returning them to the user.

We implement the query pipeline as a [AWS Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-statemachines.html) [AWS Step Functions](https://aws.amazon.com/step-functions/) state machine, managing the query pipeline for each bucket as a Lambda pipeline, as well as a single Lambda pipeline at the end of the pipeline, aggregating the data and writing the resulting ANNs to a .csv file on Amazon S3. However, this pipeline could also be implemented as a series of AWS Batch pipelines, or even run locally. The following code snippet shows how a pipeline assigned to a bucket could run an ANN query on one of the database's buckets, requesting and running only on . As detailed earlier in the data collection section, we use the asynchronous LanceDB API and package version .pandaslancedbpython \>= 3.9lancedb0.21.1

from typing import List, Iterable

import asyncio

import lancedb

import pandas

import random

async def run_query_async(

lancedb_s3_uri: str,

table_name: str,

q_vec: List\[float\],

k: int,

vec_col: str,

n_probes: int,

refine_factor: int,

) \-\> pandas.DataFrame:

"""Run a query on a LanceDB table.

Args:

lancedb_s3_uri (str): S3 URI of the LanceDB database.

table_name (str): Name of the table to query.

q_vec (List\[float\]): Query vector.

k (int): Number of nearest neighbors to return.

vec_col (str): Column name of the vector column.

n_probes (int): Number of probes to use for the query.

refine_factor (int): Refine factor for the query.

Returns:

pandas.DataFrame: DataFrame containing the approximate closest

neighbors to the query vector.

"""

\# open a connection to the database and table

with await lancedb.connect_async(

lancedb_s3_uri, storage_options\={"timeout": "120s"}

) as db_connection:

with await db_connection.open_table(table_name) as table_connection:

\# query the approximate nearest neighbors to thequery vector

df \= (

await table_connection.query()

.nearest_to(q_vec)

.column(vec_col)

.nprobes(n_probes)

.refine_factor(refine_factor)

.limit(k)

.distance_type("cosine")

.to_pandas()

)

return df

\# query the example bucket we produced in the last section

bucket1_df \= asyncio.run(

snippets.run_query_async(

lancedb_s3_uri\="s3://mg-analysis/owen/20250415_lancedb_snippet_testing/test_db/",

table_name\="bucket1",

q_vec\=\[random.random() for \_ in range(960)\],

k\=3,

vec_col\="embedding",

n_probes\=1,

refine_factor\=1,

)

)

The previous query will return a panda DataFrame with the following structure:

| embedding                  | identification | \_distance |
| :------------------------- | :------------- | :--------- |
| \[-5.124435, 4.242000, …\] | id_1           | 0.000000   |
| \[-5.783999, 4.340500, …\] | id_2           | 0.001000   |
| \[-6.932943, 3.394850, …\] | id_3           | 0.04020    |

Where, embeddingcolumn contains the vector representation of the nearest neighbors, idcolumn contains their IDs, and \_distancecolumn contains their cosine distance to the queried vector.

After each bucket is queried independently on the nodes and each bucket returns a nearest neighbor DataFrame, the results must be merged and aggregated to return to the user. The following code snippet shows how you can do this.

def aggregate_nearest_neighbors(

dfs: List\[pandas.DataFrame\], k: int

):

"""Aggregate the nearest neighbors for each query vector.

Args:

dfs (List\[pandas.DataFrame\]): A list of DataFrames containing the

nearest neighbors queried from each bucket.

k (int): The number of nearest neighbors to aggregate.

Returns:

pd.DataFrame: A DataFrame with the aggregated nearest neighbors.

"""

\# concatenate the DataFrames and get the top k nearest neighbors

return (

pandas.concat(dfs, ignore_index\=True)

.sort_values(by\=\["\_distance"\], ascending\=True)

.reset_index(drop\=True)

.head(k)

)

\# add the dataframes from querying each bucket to a list

dfs \= \[bucket1_df, bucket2_df, bucket3_df, bucket4_df, bucket_5\]

\# aggregate the nearest neighbors across all buckets

nearest_neighbors_all_buckets_df \= aggregate_nearest_neighbors(dfs, 5)

## **Optimizing for Large Query Batches**

While querying LanceDB databases directly from S3 object stores on Lambda works well when querying ANNs of one or a few query vectors, some use cases may require querying thousands or even millions of vectors.

One solution we found that scales well with large query batches is to modify the previous query implementation so that it first downloads one of the database buckets to local storage, then queries it locally using the LanceDB API. Since database buckets can have a large storage footprint, this implementation is better suited for AWS Batch jobs than Lambda, and we recommend using optimized instance storage (e.g., i4i instances) instead of EBS volumes. After all query Batch jobs complete, a final job can aggregate their results before returning them to the user. Coordinating parallel query and aggregation jobs can be done using [Nextflow](https://www.nextflow.io/docs/latest/index.html) . While this implementation will have significantly higher overhead and latency when offloading buckets to disk, it can handle larger query batches more efficiently and still does not require the database to run continuously on the server.

## **Benchmark Results**

The indexing strategy and database sharding size depend on your individual performance needs. Consider the following general optimization guidelines when customizing to your use case.

A sample database generated by Metagenomi consists of 3.5 billion AMPLIFY-generated embedding vectors, of size 960\. Ingesting and indexing these 3.5 billion embedding vectors into 200 million-vector chunks on i4i.8xlarge instances took a total of 108 compute hours. Since this solution is serverless and can be queried directly from the S3 object store, the only fixed cost of this database is the storage space on Amazon S3 (for an indexed database of 3.5 billion vectors, this is approximately 12.9 TB). Lambda queries can be an extremely low-cost query solution, with many queries costing only a few percent.

In general, larger database partitions will reduce query costs but will result in longer runtimes and indexing times. We recommend scaling up the database partition to the maximum size that achieves acceptable query return times for a givent single splits, while considering parallelization constraints such as the maximum number of concurrent Lambda functions running. Metagenomi has determined that database splits of 200 million vectors at a time provide the optimal balance of cost and runtime for both small and large queries. We recommend using and indexing on storage-optimized instances, such as those in the i4i family, for optimal performance and cost savings. If the query is executed on an instance that uses a disk-based database (as opposed to Lambda and Amazon S3), we also recommend using storage-optimized instances for the queries. We have found that Lambda deployments can quickly handle single queries requiring up to 50,000 ANNs or multiple queries of up to 100 sequences with less than 5 ANNs. The runtime increases linearly with the number of ANNs requested, as shown in the following graph.

![The line graph shows the query runtime increasing with the number of nearest neighbors]![alt text](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2025/08/28/metrics-graph-lance.png)]

## **Conclusion**

In this paper, we demonstrated how Metagenomi can store and query billions of embedded proteins at low cost using LanceDB deployed with Amazon S3 and AWS Lambda. This work extends Metagenomi’s patient-oriented mission of creating curative genomic medicines by accelerating our discovery and engineering platform. Rapid access to the ANN embedding space of a query protein in seconds has enabled the integration of rapid search methods into our extensive analytical workflows, accelerating the discovery of several diverse and novel enzyme families, and supporting protein engineering efforts by providing scientists with methods to generate and search embeddings quickly. As Metagenomi continues to rapidly expand its protein and DNA databases, horizontal scaling facilitated by database partitions that can be indexed and searched in parallel facilitates an embedding database solution that can scale to future needs.

The solution presented in this paper focuses on vectors generated by the protein [large language model](https://aws.amazon.com/what-is/large-language-model/) but can be applied to other vectorized datasets. To learn more about LanceDB integration with Amazon S3, please refer to the [LanceDB documentation](https://lancedb.github.io/lancedb/).

References

1. [Fournier, Quentin et al. “Protein language models: is scaling necessary?.” bioRxiv (2024): 2024-09.](https://www.biorxiv.org/content/10.1101/2024.09.23.614603v1)

---

### **About the authors**

<div style="display: flex; align-items: flex-start; gap: 20px; border: 1px solid #ddd; padding: 20px; border-radius: 8px;">

<!-- PHOTO -->

<img src="https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2025/08/28/audra-devoto-metagenomi.jpeg" 
alt="Audra Devoto"
width="150"
style="border-radius: 4px;" />

<!-- CONTENT -->
<div style="max-width: 600px;">

### **Audra Devoto**

Audra is a data scientist with a background in metagenomics and years of experience working with large genomics datasets on AWS. At Metagenomi, she builds infrastructure to support large-scale analysis projects and enables the discovery of new enzymes from MGXdb.

</div>

</div>

###

<div style="display: flex; align-items: flex-start; gap: 20px; border: 1px solid #ddd; padding: 20px; border-radius: 8px; margin-bottom: 20px;">

<img src="https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2025/08/28/chris-brown-metagenomi.jpeg" 
alt="Christopher Brown" 
width="150" 
style="border-radius: 4px;" />

<div style="max-width: 600px;">

### **Christopher Brown**

Dr. Christopher Brown is the Discovery team leader at Metagenomi. He is an experienced scientist and expert in the field of metagenomics, and has led the discovery and characterization of many novel enzyme systems for gene editing applications.

</div>

</div>

###

<div style="display: flex; align-items: flex-start; gap: 20px; border: 1px solid #ddd; padding: 20px; border-radius: 8px; margin-bottom: 20px;">

<img src="https://d2908q01vomqb2.cloudfront.net/fb644351560d8296fe6da332236b1f8d61b2828a/2024/04/05/Patrick-OConnor.jpeg" 
alt="Patrick O'Connor" 
width="150" 
style="border-radius: 4px;" />

<div style="max-width: 600px;">

### **Patrick O'Connor**

Patrick is a Global Senior AI Prototype Engineer at AWS, where he builds advanced generative AI solutions and end-to-end prototypes in the cloud. He specializes in deploying large language models and distributed AI systems, and applies his expertisen expertise in IoT, serverless technology, and high-performance computing to solve complex enterprise challenges.

</div>

</div>

###

<div style="display: flex; align-items: flex-start; gap: 20px; border: 1px solid #ddd; padding: 20px; border-radius: 8px; margin-bottom: 20px;">

<img src="https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2025/08/28/owen-janson-metagenomi.jpeg"
alt="Owen Janson"
width="150"
style="border-radius: 4px;" />

<div style="max-width: 600px;">

### **Owen Janson**

Owen is a Bioinformatics Engineer at Metagenomi, building tools and cloud infrastructure to support the analysis of massive genomic datasets.

</div>

</div>

###

<div style="display: flex; align-items: flex-start; gap: 20px; border: 1px solid #ddd; padding: 20px; border-radius: 8px; margin-bottom: 20px;">

<img src="https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2025/09/16/Pavel.jpg" 
alt="Pavel Novichkov" 
width="150" 
style="border-radius: 4px;" />

<div style="max-width: 600px;">

### **Pavel Novichkov**

Dr. Pavel Novichkov is a Senior Solutions Architect at AWS, specializing in genomics and life sciences. He has over 15 years of experience in bioinformatics and cloud development, helping healthcare and life sciences startups design and deploy cloud solutions on AWS. He completed his postdoctoral research at the National Center for Biotechnology Information (NIH) and worked as a Computational Research Scientist at Berkeley Lab for over 12 years, where he co-developed cutting-edge NGS technology, recognized as one of the top 90 breakthroughs in Berkeley Lab's history.

</div>

</div>
