# IMDb-Data-Pipeline-and-Analysis

 
### Navigating through AWS Athena, AWS S3 and AWS Glue
- I went over to AWS Athena because I heard I could write queries on it.
- I realized I needed a Database to at least run queries on and an AWS S3 bucket to store query results in, that made sense.
- So I marched on to AWS S3 and created a bucket. 
- Don't forget to Enable Versioning for backup and tracking changes while setting up a bucket.
- I was supposed to return to Athena settings to select the newly created bucket location but it was done automatically for me anyway.
- In S3, I had to create a folder in the bucket and other sub-folders where I uploaded respective entities from the IMDB Dataset - we're talking Gigabytes.
- I marched on to the *AWS Glue* - I'm outright falling for the AWS technologies naming convention.
- Apparently, I needed a thing that will scan the data uploaded in the S3 Bucket and create tables in the Glue Catalog - it's called a *Crawler*. Phew.
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
- I ran the *Crawler* 3 times so the 5 imdb entites appeared 3 times each, making it a total of 15 tables.
- I DROPped unwanted tables directly from the query editor in Athena. hehe.

### Data Wrangling 
#### SQL Views 
##### top_rated_movies: This view joins _title.basics_ with _title.ratings_ to display movies with a rating above 8.0.
    CREATE VIEW top_rated_movies AS
    SELECT tb.primaryTitle, tb.genres, tr.averageRating, tr.numVotes
    FROM title_basics tb
    JOIN title_ratings tr
    ON tb.tconst = tr.tconst
    WHERE tb.titleType = 'movie' AND tr.averageRating >= 8.0;
##### non-adult_movies: This view filters out adult content from the dataset for safe analysis.
    CREATE VIEW non_adult_titles AS
    SELECT *
    FROM title_basics
    WHERE isAdult = 0;
##### active_tv_series: Creates a view for TV series that started but have no end year.
    CREATE VIEW active_tv_series AS
    SELECT *
    FROM title_basics
    WHERE titleType = 'tvSeries' AND endYear IS NULL;
    
#### Creating New Tables (Stored as Parquet in S3)
##### top_voted_titles: Analyze the most popular titles based on the number of votes.
    - CREATE TABLE top_voted_titles
    WITH (
        external_location = 's3://imdb-athena-results/processed-data/top_voted_titles/',
        format = 'Parquet'  
    ) AS
    SELECT tb.primaryTitle, tr.averageRating, tr.numVotes
    FROM title_basics tb
    JOIN title_ratings tr
    ON tb.tconst = tr.tconst
    ORDER BY tr.numVotes DESC
    LIMIT 100;
##### movie_cast_details: Combine cast/crew information with movie details for analysis.
    CREATE TABLE movie_cast_details
    WITH (
        external_location = 's3://imdb-athena-results/processed-data/movie_cast_details/',
        format = 'Parquet'
    ) AS
    SELECT tb.primaryTitle, tp.category, tp.nconst, tp.characters
    FROM title_basics tb
    JOIN title_principals tp
    ON tb.tconst = tp.tconst
    WHERE tb.titleType = 'movie';
