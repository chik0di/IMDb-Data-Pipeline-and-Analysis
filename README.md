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
