# IMDb-data-pipeline-and-analysis


### Procedure
- I went over to AWS Athena because I heard I could write queries on it.
- Athena goes "Psych!" - said I need a Database to at least run queries on and an AWS S3 bucket to store query results in, tbf that made sense.
- So I marched on to S3 and created a bucket. 
- Don't forget to Enable Versioning for backup and tracking changes while setting up a bucket.
- I was supposed to return to Athena settings to select the newly created bucket location but it was done automatically for me anyway.
- In S3, I had to create a folder in the bucket and other sub-folders where I uploaded respective entities from the IMDB Dataset - we're talking Gigabytes.
- I marched on to the *AWS Glue* - I'm outright falling for the AWS technologies naming.
- Apparently I needed a thing that will scan the data uploaded in the S3 Bucket and create tables in the Glue Catalog - it's called a *Crawler*. Phew.
- A crawler connects to a data store (S3 bucket), progresses through a prioritized list of classifiers to determine the schema for your data, and then creates metadata tables in your data catalog.
- While setting up the *Crawler* -
    - Data Store: S3 → Point to your S3 path, (s3://bucket-name/imdb-data/).
    - IAM Role: Create a new role (e.g., AWSGlueServiceRole-imdb) with S3, Glue, and Athena permissions.
    - Run Frequency: On-Demand (manual run).
    - Output Database: Create a new database (imdb_db).
        - I was redirected to choose/create a new Database where the crawled data will be stored, it was quite straightforward.
- Start the crawler → It will scan the data and create tables in the Glue Catalog.

![Crawler run](https://github.com/user-attachments/assets/1c8e2627-a7d7-44e4-a15d-26c7213a5046)

- *Crawler Complete*
- Remember *Athena* sent me to *S3* to create a bucket and eventually a Crawler on *Glue*
- I returned to *Athena* and set up the db (It was automatically chosen for me)
- I ran the *Crawler* 3 times so the 5 imdb entites appeared 3 times each making it a total of 15 tables.
- I DROPped unwanted tables directly from the query editor in Athena
