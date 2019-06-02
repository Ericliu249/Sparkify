# Data Modeling with PostgresSQL for a Music App
- Author: Yang Liu
- Contact: eric.liu.249@gmail.com

## Backgroud

A startup called Sparkify wanted to analyze the data they've been collecting on songs and user activity on their new music streaming app. The analytics team were particularly interested in understanding what songs users are listening to. However, they did't have an easy way to query their data, which resided in a directory of JSON logs on user activity on the app, as well as a directory with JSON metadata on the songs in their app.

## Objective

As a data engineer, my role is to create a Postgres database with tables designed to optimize queries on song play analysis. My tasks included creating a database schema and building ETL pipeline for this analysis as well as testing the database and ETL pipeline by running queries given to me by the analytics team from Sparkify and comparing the results with their expected results.

## Dependencies

- psycopg2
- Python 3.5+
- pandas
- os
- glob

## Data Sources

### Song Dataset

The first dataset is a subset of real data from the [Million Song Dataset](http://millionsongdataset.com/). Each file is in JSON format and contains metadata about a song and the artist of that song. The files are partitioned by the first three letters of each song's track ID. For example, here are filepaths to two files in this dataset.

- song_data/A/B/C/TRABCEI128F424C983.json
- song_data/A/A/B/TRAABJL12903CDCF1A.json

And below is an example of what a single song file, TRAABJL12903CDCF1A.json, looks like.

{"num_songs": 1, "artist_id": "ARJIE2Y1187B994AB7", "artist_latitude": null, "artist_longitude": null, "artist_location": "", "artist_name": "Line Renaud", "song_id": "SOUPIRU12A6D4FA1E1", "title": "Der Kleine Dompfaff", "duration": 152.92036, "year": 0}

### Log Dataset
The second dataset consists of log files in JSON format generated by this event simulator based on the songs in the dataset above. These simulate activity logs from a music streaming app based on specified configurations.

The log files in the dataset you'll be working with are partitioned by year and month. For example, here are filepaths to two files in this dataset.

- log_data/2018/11/2018-11-12-events.json
- log_data/2018/11/2018-11-13-events.json

And below is an example of what the data in a log file, 2018-11-12-events.json, looks like.

![example4](https://github.com/Ericliu249/Sparkify/blob/master/pic/example4.png)

## Schema for Song Play Analysis
Using the song and log datasets, you'll need to create a star schema optimized for queries on song play analysis. This includes the following tables.

### Fact Table
1. **songplays** - records in log data associated with song plays i.e. records with page NextSong
     - *songplay_id, start_time, user_id, level, song_id, artist_id, session_id, location, user_agent*

### Dimension Tables
2. **users** - users in the app
     - *user_id, first_name, last_name, gender, level*
     
3. **songs** - songs in music database
     - *song_id, title, artist_id, year, duration*

4. **artists** - artists in music database
     - *artist_id, name, location, latitude, longitude*
     
5. **time** - timestamps of records in **songplays** broken down into specific units
     - *start_time, hour, day, week, month, year, weekday*
     
## Guideline
1. **test.ipynb** : displays the first few rows of each table to check the database.
2. **create_tables.py** : drops and creates the tables. Run this file to reset the tables before each time run the ETL scripts.
3. **etl.ipynb** : reads and processes a single file from song_data and log_data and loads the data into the tables. This notebook contains detailed instructions on the ETL process for each of the tables.
4. **etl.py** : reads and processes files from song_data and log_data and loads them into the tables.  This script was created based on the work in the ETL notebook.
5. **sql_queries.py** : contains all sql queries, and is imported into the last three files above.

## Project Steps

### Create Tables
1. Write **CREATE** statements in **sql_queries.py** to create each table.
2. Write **DROP** statements in **sql_queries.py** to drop each table if it exists.
3. Run **create_tables.py** to create the database and tables.
4. Run **test.ipynb** to confirm the creation of the tables with the correct columns. Make sure to click "Kernel-Restart/Shutdown" to close the connection to the database after running this notebook.

### Build ETL Pipeline
Developed ETL processes for each table in the **etl.py**. 

1. Process song_data
    - Perform ETL on the first dataset, **song_data**, to create the **songs** and **artists** dimensional tables.
    - Implement the **song_table_insert** and **artist_table_insert** queries in **sql_queries.py** and run the cell below to insert records into the **songs** and **artists** tables. 
    
2. Process log_data
    - Perform ETL on the second dataset, **log_data**, to create the **time** and **users** dimensional tables, as well as the **songplays** fact table.
    - Implement the **song_select** query in **sql_queries.py** to find the song ID and artist ID based on the title, artist name, and duration of a song.
    - Select the timestamp, user ID, level, song ID, artist ID, session ID, location, and user agent and set to **songplay_data**
    - Insert Records into the **time**, **users** and **Songplays** Tables
    
Remember to run **create_tables.py** before running **etl.py** to reset the tables. Run **test.ipynb** to confirm the records were successfully inserted into each table.
