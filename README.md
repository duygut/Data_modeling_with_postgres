# Project: Udacity - Data Modeling with Postgres 

A startup called Sparkify wants to analyze the data they've been collecting on songs and user activity on their new music streaming app, particularly interested in understanding what songs users are listening to. 

In that project below steps implemented:

1. Two datasets which are in JSON format has been queried
2. Created Postgres schema and ETL pipeline to optimize queries
3. Test the schema and pipeline with the test file


Below python libraries have been used.

* glob
* psycopg2
* pandas 

## 1. Datasets

Songs dataset contain information about:
   * artist_id
   * artist_latitude
   * artist_location
   * artist_longitude
   * artist_name
   * duration
   * num_songs
   * song_id
   * title
   * year

Log dataset contain information about:

    * artist
    * auth
    * firstName
    * gender
    * itemInSession
    * lastName
    * length
    * level
    * location
    * method
    * page
    * registration
    * sessionId
    * song
    * status
    * ts
    * userAgent
    * userId

## 2. Song Play Analysis Schema

Star Schema has been created for queries on song play analysis.

 **Fact Table** 
 
 - **Songplays** : records in log data associated with song plays i.e. records with page `NextSong`
 
**Dimension Tables** 

- **users** - users in the app
- **songs** - songs in music database
- **artists** - artists in music database
- **time** - timestamps of records in songplays broken down into specific units


## 3. Project Steps

1. SQL queries such as 'DROP TABLE', 'CREATE TABLE', 'INSERT INTO' have been created in `sql_queries.py` file. PRIMARY key has ben defined for each table. Avoid the duplicate row error, `ON CONFLICT (id) DO NOTHING` has been added. 
| songplay_table_create = (""" CREATE TABLE IF NOT EXISTS songplays (songplay_id SERIAL PRIMARY KEY, 	|
|-	|
| start_time TIMESTAMP, 	|
| user_id VARCHAR, 	|
| level VARCHAR, 	|
| song_id VARCHAR, 	|
| artist_id VARCHAR, 	|
| session_id VARCHAR, 	|
| location VARCHAR, 	|
| user_agent VARCHAR); 	|
| """) 	|

2. Run `create_tables.py` to reset the tables before each time to run ETL scripts

| def create_database():                                                                    |
|-------------------------------------------------------------------------------------------|
| """                                                                                       |
| - Creates and connects to the sparkifydb                                                  |
| - Returns the connection and cursor to sparkifydb                                         |
| """                                                                                       |
| # connect to default database                                                             |
| conn = psycopg2.connect("host=127.0.0.1 dbname=studentdb user=student password=student")  |
| conn.set_session(autocommit=True)                                                         |
| cur = conn.cursor()                                                                       |
| # create sparkify database with UTF8 encoding                                             |
| cur.execute("DROP DATABASE IF EXISTS sparkifydb")                                         |
| cur.execute("CREATE DATABASE sparkifydb WITH ENCODING 'utf8' TEMPLATE template0")         |
| # close connection to default database                                                    |
| conn.close()                                                                              |
| # connect to sparkify database                                                            |
| conn = psycopg2.connect("host=127.0.0.1 dbname=sparkifydb user=student password=student") |
| cur = conn.cursor()                                                                       |
| return cur, conn                                                                          |


3. With `etl.ipynb` notebook, single file from `song_data` and `log_data` have been read and ETL has been processed. 
Song Data:
|          artist_id | artist_latitude | artist_location | artist_longitude | artist_name |  duration | num_songs |            song_id |            title | year |
|-------------------:|----------------:|----------------:|-----------------:|------------:|----------:|----------:|-------------------:|-----------------:|-----:|
| ARD7TVE1187B99BFB1 |             NaN | California - LA |              NaN |      Casual | 218.93179 |         1 | SOMZWCG12A8C13C480 | I Didn't Mean To |    0 |

Log Data:
|   |  artist |      auth | firstName | gender | itemInSession | lastName |    length | level |                    location | method |     page |  registration | sessionId |         song | status |            ts |                                         userAgent | userId |
|---|--------:|----------:|----------:|-------:|--------------:|---------:|----------:|------:|----------------------------:|-------:|---------:|--------------:|----------:|-------------:|-------:|--------------:|--------------------------------------------------:|-------:|
| 1 |    None | Logged In |    Kaylee |      F |             0 |  Summers |       NaN |  free | Phoenix-Mesa-Scottsdale, AZ |    GET |     Home | 1540344794796 |       139 |         None |    200 | 1541106106796 | "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebK... |      8 |
| 2 | Des'ree | Logged In |    Kaylee |      F |             1 |  Summers | 246.30812 |  free | Phoenix-Mesa-Scottsdale, AZ |    PUT | NextSong | 1540344794796 |       139 | You Gotta Be |    200 | 1541106106796 | "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebK... |      8 |
| 3 |    None | Logged In |    Kaylee |      F |             2 |  Summers |       NaN |  free | Phoenix-Mesa-Scottsdale, AZ |    GET |  Upgrade | 1540344794796 |       139 |         None |    200 | 1541106132796 | "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebK... |      8 |
| 4 | Mr Oizo | Logged In |    Kaylee |      F |             3 |  Summers | 144.03873 |  free | Phoenix-Mesa-Scottsdale, AZ |    PUT | NextSong | 1540344794796 |       139 |      Flat 55 |    200 | 1541106352796 | "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebK... |      8 |

4. Run `test.ipynb` file to test schema and pipeline
Song Play
| songplay_id |                 start_time | user_id | level | song_id | artist_id | session_id |                    location |                                                                                                      user_agent |
|------------:|---------------------------:|--------:|------:|--------:|----------:|-----------:|----------------------------:|----------------------------------------------------------------------------------------------------------------:|
|           1 | 2018-11-01 21:01:46.796000 |       8 |  free |    None |      None |        139 | Phoenix-Mesa-Scottsdale, AZ | "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/35.0.1916.153 Safari/537.36" |
|           2 | 2018-11-01 21:05:52.796000 |       8 |  free |    None |      None |        139 | Phoenix-Mesa-Scottsdale, AZ | "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/35.0.1916.153 Safari/537.36" |
|           3 | 2018-11-01 21:08:16.796000 |       8 |  free |    None |      None |        139 | Phoenix-Mesa-Scottsdale, AZ | "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/35.0.1916.153 Safari/537.36" |
|           4 | 2018-11-01 21:11:13.796000 |       8 |  free |    None |      None |        139 | Phoenix-Mesa-Scottsdale, AZ | "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/35.0.1916.153 Safari/537.36" |
|           5 | 2018-11-01 21:17:33.796000 |       8 |  free |    None |      None |        139 | Phoenix-Mesa-Scottsdale, AZ | "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/35.0.1916.153 Safari/537.36" |

User

| user_id | first_name | last_name | gender | level |
|--------:|-----------:|----------:|-------:|------:|
|       8 |     Kaylee |   Summers |      F |  free |
|      10 |     Sylvie |      Cruz |      F |  free |
|      26 |       Ryan |     Smith |      M |  free |
|     101 |     Jayden |       Fox |      M |  free |

Songs

|            song_id |            title |          artist_id | year |  duration |
|-------------------:|-----------------:|-------------------:|-----:|----------:|
| SOMZWCG12A8C13C480 | I Didn't Mean To | ARD7TVE1187B99BFB1 |    0 | 218.93179 |

Artists

|          artist_id |   name |        location | latitude | longitude |
|-------------------:|-------:|----------------:|---------:|----------:|
| ARD7TVE1187B99BFB1 | Casual | California - LA |      nan |       nan |

Time

|                 start_time | hour | day | week | month | year | weekday |
|---------------------------:|-----:|----:|-----:|------:|-----:|--------:|
| 2018-11-01 21:01:46.796000 |   21 |   1 |   44 |    11 | 2018 |       3 |
| 2018-11-01 21:05:52.796000 |   21 |   1 |   44 |    11 | 2018 |       3 |
| 2018-11-01 21:08:16.796000 |   21 |   1 |   44 |    11 | 2018 |       3 |
| 2018-11-01 21:11:13.796000 |   21 |   1 |   44 |    11 | 2018 |       3 |
| 2018-11-01 21:17:33.796000 |   21 |   1 |   44 |    11 | 2018 |       3 |

5. After the test file pass, ETL process commands copied to the `etl.py` file to process same steps for the entire dataset. 
