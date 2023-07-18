# TEST

This is a technical test for a data engineer position, in which it is asked to do the following:

* ( *5%* ) Obtain the file from [this link](!https://gist.github.com/juanriaza/9b6f7ab3fc1cbda81100fa5d32512fd8/raw/a61db2d8d81789d5c7040eacee7b225bc2b2aa7f/data.csv.bz2)

* ( *15%* ) Through command line, set up a SQLite database.

* ( *30%* ) Using `sqlite3` respond to the following bussiness cases:

  * Num rental properties from each client by postal code
  * Obtain the following Barcelona's postal codes:
    * Cheapest €/m2 
    * Most expensive €/m2
    * Client with the oldest real state portfolio
    * City with the most expensive SU (Suelo Urbanizable).

* ( *35%* ) Answer the previous cases using `pandas`

* ( *10%* ) An investor wants to know interesting properties to invest by its costal code. Add to the previous dataframe the following columns:

  * over_valued

  * normal

  * under_valued

    Which postal code has the most under_valued properties?

* ( *5%* ) Save the resulted dataframe as a gzip json file.

Bonus points : 

* Interesting viz
* Explain how have you handle the data exploration.

----

### Setup

* **Environment**: for this test a python virtual env. was set up the following way: 

  * Create a new environtment within at the repository root level as a hidden folder:

    ```
    python -m venv .env/
    ```

  * Then install the project dependencies

    ```
    pip install -r requirements.txt
    ```

* **SQLite3**: I didn't had installed the package, therefore I had to install it in a terminal with ``sudo apt install sqlite3``.

  * Then, I have created a new database for this project, sqlite lite creates a file where the database is stored:

    ```
    sqlite3 database.db
    ```

  * Once the data has been download and stored in the `data`folder, I have entered the database through the command line and executed the following to ingest the input file:
  
    ``` 
    sqlite> .mode csv
    sqlite> .separator ,
    sqlite> .tables     -- check no tables exist in the database
    sqlite> create table t (
            customer_id		    VARCHAR(25)  NOT NULL,
            city				VARCHAR(128),
            typology			VARCHAR(128),
            subtypology		    VARCHAR(128),
            transaction_type    VARCHAR(64),
            price				INTEGER(64),
            zipcode			    VARCHAR(5),
            bedrooms			INTEGER(32),
            bathrooms			INTEGER(32),
            area				INTEGER(32),
            area_land			INTEGER(32),
            is_professional	    BOOL,
            date_added		    TIMESTAMP
    ); -- just the ID cannot be null since data hasn't been explored yet
    sqlite> . tables -- check the table t exists
    sqlite> .import data/data.csv t --skip 1
    sqlite> .headers on 
    sqlite> .mode column
    sqlite> select * from t limit 10;
    ```
  
    The previous list of commands do the following:
  
    * Creates a new table checking before / after the current existing tables.
  
    * Stablish the ingestion setup (mode and separator)
  
    * Imports the data from `data.csv`into `t` skipping the first line, which corresponds with the header.
  
    * Change the setup to pretty print a simple query.
  
      
  
    The last query shows the following result (just showing first and last columns):
  
    ```
    customer_id                 ...   date_added         
    --------------------------  ...   -------------------
    39323032373531333936323630  ...   2012-05-18T12:26:00
    39323032373532323235313938  ...   2014-04-01T11:23:00
    39323032373534303438333537  ...   2014-06-02T09:38:00
    39323032373531373435393038  ...   2014-12-11T00:01:00
    39323032373534353337323338  ...   2015-04-08T10:26:00
    39323032373531323930363130  ...   2015-08-28T19:39:00
    39323032373531353539373530  ...   2016-03-09T18:09:00
    39323032373532373237373132  ...   2016-05-31T14:13:00
    39323032373531353332383633  ...   2016-06-06T17:19:00
    39323032373532333432353634  ...   2016-08-30T13:56:00
    ```
  
    Once the data was loaded, I have exited the database in the command line with `Ctrl+D`and used `%sql`to connect to the database inside the notebook.
  
  ---
  
  ### Analysis:
  
  * Most data is from professional customers
  
  * There are the most common typologies, in the case of housing, the number of bedrooms and bathrooms is provided.
  
  * .describe() is used to see the range of values of numerical columns, while value_counts() is used for categorical values
  
  * .info() shows null data within the sample and dtype when opening
  
  * A couple of viz are given:
  
    * Distribution of price (lognormal unit price and price) by typology
    * Number of properties by zipcode for each customer
  
  * Given that housing typologies are so diverse and usually have different range of prices, a combination of both categories have been explored for the question of the market opportinities.
  
    