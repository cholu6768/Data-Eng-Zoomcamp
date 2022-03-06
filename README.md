# Data-Eng-Zoomcamp
Assigments for the Data Engineering Zoomcamp

# Homework Questions Week 1
<details>
	
## Question 3. Count records

**How many taxi trips were there on January 15?
Consider only trips that started on January 15.**

```sql
SELECT 
	COUNT(tpep_pickup_datetime)
FROM public.yellow_taxi_data
WHERE 
	tpep_pickup_datetime >= '2021-01-15 00:00:00' AND
	tpep_pickup_datetime < '2021-01-16 00:00:00'
```

| trips |
|-------|
| 53024 |

**There were 53,024 trips on January 15 of 2021.**

## Question 4. Largest tip for each day

**Find the largest tip for each day. On which day it was the largest tip in January?**

**Use the pick up time for your calculations.**

```sql
SELECT
	DATE(tpep_pickup_datetime),
	MAX(tip_amount) AS max_tip_day
FROM public.yellow_taxi_data 
GROUP BY DATE(tpep_pickup_datetime)
HAVING DATE(tpep_pickup_datetime) < '2021-02-01'
ORDER BY max_tip_day DESC;
```
| date         | max_tip_day |
|--------------|-------------|
| "2021-01-20" | 1140.44     |
| "2021-01-04" | 696.48      |
| "2021-01-03" | 369.4       |
| "2021-01-26" | 250         |
| "2021-01-09" | 230         |
| "2021-01-19" | 200.8       |
| "2021-01-30" | 199.12      |
| "2021-01-12" | 192.61      |
| "2021-01-21" | 166         |
| "2021-01-01" | 158         |
| "2021-01-05" | 151         |
| "2021-01-11" | 145         |
| "2021-01-24" | 122         |
| "2021-01-02" | 109.15      |
| "2021-01-31" | 108.5       |
| "2021-01-25" | 100.16      |
| "2021-01-23" | 100         |
| "2021-01-13" | 100         |
| "2021-01-16" | 100         |
| "2021-01-27" | 100         |
| "2021-01-06" | 100         |
| "2021-01-08" | 100         |
| "2021-01-15" | 99          |
| "2021-01-07" | 95          |
| "2021-01-14" | 95          |
| "2021-01-22" | 92.55       |
| "2021-01-10" | 91          |
| "2021-01-18" | 90          |
| "2021-01-28" | 77.14       |
| "2021-01-29" | 75          |
| "2021-01-17" | 65          |
| "2020-12-31" | 4.08        |
| "2008-12-31" | 0           |
| "2009-01-01" | 0           |

**The largest tip was on January 20th of 2021. The given tip was 1140.44$.**

## Question 5. Most popular destination
**What was the most popular destination for passengers picked up in central park on January 14?**

**Use the pick up time for your calculations.**

**Enter the zone name (not id). If the zone name is unknown (missing), write "Unknown"**

```sql
WITH taxi_pu AS(
	SELECT
		yellow_taxi_data.index,
		DATE(yellow_taxi_data.tpep_pickup_datetime) AS date_pu,
		yellow_taxi_data.pu_location_id AS pu_location_id,
		CASE
    		WHEN taxi_zone.zone IS NULL THEN 'Unknown'
    		ELSE taxi_zone.zone
		END AS pu_zone
	FROM public.yellow_taxi_data
	LEFT JOIN taxi_zone
		ON yellow_taxi_data.pu_location_id = taxi_zone.location_id
	WHERE 
		DATE(yellow_taxi_data.tpep_pickup_datetime) = '2021-01-14' AND
		taxi_zone.zone='Central Park'
), 
taxi_do AS (
	SELECT
		yellow_taxi_data.index,
		DATE(yellow_taxi_data.tpep_dropoff_datetime) AS date_do,
		yellow_taxi_data.do_location_id AS do_location_id,
		CASE
    		WHEN taxi_zone.zone IS NULL THEN 'Unknown'
    		ELSE taxi_zone.zone
		END AS do_zone
	FROM public.yellow_taxi_data
	LEFT JOIN taxi_zone
		ON yellow_taxi_data.do_location_id = taxi_zone.location_id
	WHERE 
		DATE(tpep_pickup_datetime) = '2021-01-14'
)

SELECT 
	taxi_pu.date_pu,
	taxi_do.date_do,
	taxi_pu.pu_zone,
	taxi_do.do_zone,
	COUNT(taxi_do.do_zone) AS frequency
FROM taxi_pu
LEFT JOIN taxi_do
	ON taxi_pu.index = taxi_do.index
GROUP BY 
	pu_zone,
	do_zone,
	taxi_pu.date_pu,
	taxi_do.date_do
ORDER BY frequency DESC;
```

| date_pu      | date_do      | pu_zone        | do_zone                          | frequency |
|--------------|--------------|----------------|----------------------------------|-----------|
| "2021-01-14" | "2021-01-14" | "Central Park" | "Upper East Side South"          | 97        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Upper East Side North"          | 94        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Lincoln Square East"            | 83        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Upper West Side North"          | 68        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Upper West Side South"          | 60        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Central Park"                   | 59        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Midtown Center"                 | 56        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Yorkville West"                 | 39        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Lenox Hill West"                | 39        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Lincoln Square West"            | 36        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Midtown North"                  | 29        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Yorkville East"                 | 25        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Manhattan Valley"               | 24        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Midtown East"                   | 22        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "East Harlem South"              | 21        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Lenox Hill East"                | 21        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Murray Hill"                    | 20        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Midtown South"                  | 19        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Clinton East"                   | 19        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Garment District"               | 18        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Union Sq"                       | 15        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "West Chelsea/Hudson Yards"      | 13        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Central Harlem"                 | 13        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "UN/Turtle Bay South"            | 12        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Sutton Place/Turtle Bay North"  | 12        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Morningside Heights"            | 11        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Little Italy/NoLiTa"            | 11        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Clinton West"                   | 10        |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Greenwich Village North"        | 9         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Times Sq/Theatre District"      | 9         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "East Harlem North"              | 8         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "West Village"                   | 8         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "East Chelsea"                   | 7         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Washington Heights South"       | 7         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Gramercy"                       | 6         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Meatpacking/West Village West"  | 5         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Central Harlem North"           | 5         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Hamilton Heights"               | 5         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Flatiron"                       | 4         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "East Village"                   | 4         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Bloomingdale"                   | 4         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "NV"                             | 3         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Steinway"                       | 3         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "TriBeCa/Civic Center"           | 3         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Washington Heights North"       | 3         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Financial District North"       | 2         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Stuy Town/Peter Cooper Village" | 2         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Penn Station/Madison Sq West"   | 2         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Sunset Park West"               | 2         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Kips Bay"                       | 2         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Hudson Sq"                      | 2         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "SoHo"                           | 2         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Greenwich Village South"        | 2         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Battery Park City"              | 2         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Manhattanville"                 | 2         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Long Island City/Hunters Point" | 2         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Lower East Side"                | 2         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Bay Ridge"                      | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Flatlands"                      | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Flatbush/Ditmas Park"           | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "East Williamsburg"              | 1         |
| "2021-01-14" | "2021-01-15" | "Central Park" | "East Harlem South"              | 1         |
| "2021-01-14" | "2021-01-15" | "Central Park" | "Yorkville West"                 | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "East Flatbush/Farragut"         | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Eastchester"                    | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Crown Heights South"            | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Williamsbridge/Olinville"       | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Windsor Terrace"                | 1         |
| "2021-01-14" | "2021-01-15" | "Central Park" | "Midtown East"                   | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Inwood"                         | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Morrisania/Melrose"             | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Jackson Heights"                | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Ocean Hill"                     | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Old Astoria"                    | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Park Slope"                     | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Pelham Bay"                     | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Seaport"                        | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Spuyten Duyvil/Kingsbridge"     | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Boerum Hill"                    | 1         |
| "2021-01-14" | "2021-01-14" | "Central Park" | "Sunnyside"                      | 1         |
| "2021-01-14" | "2021-01-15" | "Central Park" | "Midtown North"                  | 1         |

**The most popular destination for passengers picked up from central park on the 14th of January of 2021 was Upper East Side South.**

## Question 6. Most expensive locations

**What's the pickup-dropoff pair with the largest average price for a ride (calculated based on total_amount)?**

**Enter two zone names separated by a slash**

**For example:**

**"Jamaica Bay / Clinton East"**

**If any of the zone names are unknown (missing), write "Unknown". For example, "Unknown / Clinton East".**

```sql
WITH taxi_pu AS(
	SELECT
		yellow_taxi_data.index,
		DATE(yellow_taxi_data.tpep_pickup_datetime) AS date_pu,
		yellow_taxi_data.pu_location_id AS pu_location_id,
		CASE
    		WHEN taxi_zone.zone IS NULL THEN 'Unknown'
    		ELSE taxi_zone.zone
		END AS pu_zone,
		yellow_taxi_data.total_amount
	FROM public.yellow_taxi_data
	LEFT JOIN taxi_zone
		ON yellow_taxi_data.pu_location_id = taxi_zone.location_id
), 
taxi_do AS (
	SELECT
		yellow_taxi_data.index,
		DATE(yellow_taxi_data.tpep_dropoff_datetime) AS date_do,
		yellow_taxi_data.do_location_id AS do_location_id,
		CASE
    		WHEN taxi_zone.zone IS NULL THEN 'Unknown'
    		ELSE taxi_zone.zone
		END AS do_zone,
		yellow_taxi_data.total_amount
	FROM public.yellow_taxi_data
	LEFT JOIN taxi_zone
		ON yellow_taxi_data.do_location_id = taxi_zone.location_id
),

trip_amount AS(
	SELECT 
		taxi_pu.pu_zone,
		taxi_do.do_zone,
		taxi_do.total_amount
	FROM taxi_pu
	LEFT JOIN taxi_do
		ON taxi_pu.index = taxi_do.index
) 

SELECT 
	CONCAT(pu_zone, ' / ', do_zone) AS pu_do_pair,
	AVG(total_amount) AS average_price
FROM trip_amount
GROUP BY 
	pu_zone,
	do_zone
ORDER BY average_price DESC
LIMIT 20;
```

| pu_do_pair                                        | average_price      |
|---------------------------------------------------|--------------------|
| "Alphabet City / Unknown"                         | 2292.4             |
| "Union Sq / Canarsie"                             | 262.85200000000003 |
| "Ocean Hill / Unknown"                            | 234.51             |
| "Long Island City/Hunters Point / Clinton East"   | 207.61             |
| "Boerum Hill / Woodside"                          | 200.3              |
| "Baisley Park / Unknown"                          | 181.4425           |
| "Bushwick South / Long Island City/Hunters Point" | 156.96             |
| "Willets Point / Unknown"                         | 154.42             |
| "Co-Op City / Dyker Heights"                      | 151.37             |
| "Rossville/Woodrow / Pelham Bay Park"             | 151                |
| "Charleston/Tottenville / Woodlawn/Wakefield"     | 149.99             |
| "Borough Park / NV"                               | 149.53             |
| "Eastchester / Charleston/Tottenville"            | 148.43333333333334 |
| "Jackson Heights / Unknown"                       | 147.91             |
| "Seaport / Unknown"                               | 145.85999999999999 |
| "Charleston/Tottenville / Eastchester"            | 145.75799999999998 |
| "Inwood / JFK Airport"                            | 145.52             |
| "Charleston/Tottenville / Co-Op City"             | 145.11363636363637 |
| "Port Richmond / Van Cortlandt Village"           | 145.1              |
| "Eltingville/Annadale/Prince's Bay / Co-Op City"  | 144.75             |

**The pickup-dropoff pair with the largest average price for a ride is Alphabet City / Unknown.**
	
</details>

# Homework Questions Week 2

<details>

## Question 1: Start date for the Yellow taxi data

**What should be the start date for this dag?**

The start date should be 2019-01-01 since we want to retrieve the yellow taxi data starting from january of 2019.

## Question 2: Frequency for the Yellow taxi data

**How often do we need to run this DAG?**

We need to run the DAG every month, since we need to retrive the taxi yellow data from every month.

![airflow task](https://github.com/cholu6768/Data-Eng-Zoomcamp/blob/main/week_2_dags/airflow_task_yellow_taxi.JPG)

### Link for the DAG script that ingests the yellow taxi data: [Click here](https://github.com/cholu6768/Data-Eng-Zoomcamp/blob/main/week_2_dags/data_ingestion_gcs_dag_yellow_taxi.py)

## Question 3: DAG for FHV Data

**How many DAG runs are green for data in 2019 after finishing everything?**

There were 12 DAG runs once everything was done.

!["airflow task"](https://github.com/cholu6768/Data-Eng-Zoomcamp/blob/main/week_2_dags/airflow_task_fhv.JPG)

### Link for the DAG script that ingests the For Hire Vehicles data: [Click here](https://github.com/cholu6768/Data-Eng-Zoomcamp/blob/main/week_2_dags/data_ingestion_gcs_dag_fhv.py)

## Question 4: DAG for Zones

**How often does it need to run?**

The DAG should only be ran once since there is only one file for the taxi zones.

### Link for the DAG script that ingests the taxi zone data: [Click here](https://github.com/cholu6768/Data-Eng-Zoomcamp/blob/main/week_2_dags/data_ingestion_gcs_dag_taxi_zone.py)
	
</details>	

# Homework Questions Week 3

<details>

## Question 1: What is count for fhv vehicles data for year 2019?

First, I created a table that had all the data from for-hire-vehicles of 2019

```sql
CREATE OR REPLACE EXTERNAL TABLE `mythic-evening-339419.trips_data_all.external_fhv_tripdata`
OPTIONS (
  format = 'PARQUET',
  uris = ['gs://dtc_data_lake_mythic-evening-339419/raw/fhv_tripdata_2019-*.parquet']
);
```

After that, I counted all the rows from the new table called external_fhv_tripdata

```sql
SELECT
    COUNT(pickup_datetime) AS num_rows
FROM mythic-evening-339419.trips_data_all.external_fhv_tripdata 
```
There are 42,084,899 rows for fhv vehicles data for the year 2019.

| num_rows |
|----------|
| 42084899 |

## Question 2: How many distinct dispatching_base_num do we have in fhv for 2019?

To know how many unique dispatching_base_num there were, I counted all the ```DISTINCT``` values of dispatching_base_num.

```sql
SELECT
    COUNT(DISTINCT dispatching_base_num) AS frequency
FROM mythic-evening-339419.trips_data_all.external_fhv_tripdata 
```
There were a total of 792 dispatching_base_num.

| frequency |
|-----------|
| 792       |

## Question 3: Best strategy to optimise, if query always filter by dropoff_datetime and order by dispatching_base_num

The best way to optimize the query would be by creating a new table with the same data but by doing a partition by the pickup_datetime and then doing a cluster by dispatching_base_num.

```sql
CREATE OR REPLACE TABLE mythic-evening-339419.trips_data_all.fhv_tripdata_partitoned_clustered
PARTITION BY DATE(pickup_datetime)
CLUSTER BY dispatching_base_num AS
SELECT * FROM mythic-evening-339419.trips_data_all.external_fhv_tripdata;
```

Let's try it out and compare with the table that is not partioned nor clustered.

```sql
SELECT
    COUNT(*) AS trips
FROM mythic-evening-339419.trips_data_all.external_fhv_tripdata 
WHERE 
    DATE(pickup_datetime) BETWEEN '2019-01-01' AND '2019-08-20'
    AND dispatching_base_num='B00009';
```

This is the information I got from processing this query: Query complete (9.5 sec elapsed, 547.5 MB processed)

Now, let's see for the table that has a partition and is clustered.

```sql
SELECT 
    COUNT(*) as trips
FROM mythic-evening-339419.trips_data_all.fhv_tripdata_partitoned_clustered
WHERE 
    DATE(pickup_datetime) BETWEEN '2019-01-01' AND '2019-08-20'
    AND dispatching_base_num='B00009';
```
This is the information I got from processing this query: Query complete (0.4 sec elapsed, 249.2 MB processed)

**We can see that the by doing the partition by pickup_datetime and clustering by dispatching_base_num, does help the processing time and less data gets processed which means less costs.**

## Question 4: What is the count, estimated and actual data processed for query which counts trip between 2019/01/01 and 2019/03/31 for dispatching_base_num B00987, B02060, B02279

First, I created the query with the requirements. 

For some reason, I could not get the estimated processed data for any of the queries. It is always loading and it never tells me the estimated processed data.

```sql
SELECT 
    COUNT(*) as trips
FROM mythic-evening-339419.trips_data_all.fhv_tripdata_partitoned_clustered
WHERE 
    DATE(pickup_datetime) BETWEEN '2019-01-01' AND '2019-03-31'
    AND dispatching_base_num IN ('B00987', 'B02279', 'B02060');
```
| trips |
|-------|
| 26647 |

Information I got from processing this query: Query complete (0.3 sec elapsed, 161.1 MB processed)

My answer did not match with the ones from the homework so I decided to choose the closest one which was: 

**Count: 26558, Estimated data processed: 400MB, Actual data processed: 155MB**

## Question 5: What will be the best partitioning or clustering strategy when filtering on dispatching_base_num and SR_Flag

Clustering by dispatching_base_num and SR_Flag was the best option. 

Partitioning can't be done on any of the two columns because dispatching_base_num is a ```STRING``` column and SR_Flag is an ```INTEGER``` column but with lots of ```NULL``` values.

```sql
CREATE OR REPLACE TABLE mythic-evening-339419.trips_data_all.fhv_tripdata_clustered
CLUSTER BY dispatching_base_num, SR_Flag AS
SELECT * FROM mythic-evening-339419.trips_data_all.external_fhv_tripdata;
```

## Question 6: What improvements can be seen by partitioning and clustering for data size less than 1 GB

There are no improvements because partitioning and clustering would creata extra metadata which as a result incur metadata reads and metadata maintenance. This would create extra costs.

## Question 7: In which format does BigQuery save data

BigQuery saves data in a columnar format. 

This format is used because when doing queries it will only read the selected columns and not the parent columns. This will give a performance improvement.

</details>

# Homework Questions Week 4

<details>

To answers these questions I created dbt models which are in another [repository](https://github.com/cholu6768/Data-Engineering-dbt-week-4). The reason was so that I kept this repository cleaner. 

## Question 1: What is the count of records in the model fact_trips after running all models with the test run variable disabled and filtering for 2019 and 2020 data only (pickup datetime)?

I counted all the rows and filtered the data between the years of 2019 and 2020.

```sql
SELECT 
    COUNT(*) AS frequency
FROM `mythic-evening-339419.dbt_fernando_chavez.fact_trips` 
WHERE 
    DATE(pickup_datetime) <= '2020-12-31' AND
    DATE(pickup_datetime) >= '2019-01-01'
```
At the end my answer was not among the ones provided in the options but I chose the closest one which was 61,635,151.

| frequency |
|-----------|
| 61451452  |

## Question 2: What is the distribution between service type filtering by years 2019 and 2020 data ? as done in the videos

After doing the pie chart, the distribution was 89.8% for the yellow trip data and 10.2% for the green trip data.

![pie chart](yellow_green_distribution.jpg)

## Question 3: What is the count of records in the model stg_fhv_tripdata after running all models with the test run variable disabled (:false)

After creating the staging model, I filtered the data from 2019 and ran the query.

```sql
SELECT 
    COUNT(*) AS frequency
FROM `mythic-evening-339419`.`trips_data_all`.`fhv_tripdata`
WHERE 
    EXTRACT(YEAR FROM pickup_datetime) IN (2019)
```	
At the end I got on the of the answers from the options shown. The answer was 42,084,899 which is the number of rows or records in that table when the pickup date was in the year of 2019.

| frequency |
|-----------|
| 42084899  |

## Question 4: What is the count of records in the model fact_fhv_trips after running all dependencies with the test run variable disabled (:false)?

Once I created the core model, i counted all the rows in the table. As my core model table already filtered the record for only 2019, i did not need to filter it again.

```sql
SELECT 
    COUNT(*) AS frequency
FROM `mythic-evening-339419.dbt_fernando_chavez.fact_fhv_trips` 
```
Once again, i got an answer from the ones in the available options. The answer was 22,676,253 records or rows in the year of 2019.

| frequency |
|-----------|
| 22676253  |

## Question 5: What is the month with the biggest amount of rides after building a tile for the fact_fhv_trips table?

As seen in the graph the month with the most trips by far was january.

![graph](fhv_trips_month.jpg)


</details>

# Homework Questions Week 5
<details>

## Question 1: Install Spark and PySpark

- **Install Spark**
- **Run PySpark**
- **Create a local spark session**
- **Execute spark.version**

```python
# Import libraries to run Pyspark
import pyspark
from pyspark.sql import SparkSession

# Create a local spark session
spark = SparkSession.builder \
    .master("local[*]") \
    .appName('test') \
    .getOrCreate()

print(spark.version)
```

## What's the output?

OUTPUT:
```python
3.0.3
```

## Question 2. HVFHW February 2021

- **Download the HVFHV data for february 2021**

- **Read it with Spark using the same schema as we did in the lessons. We will use this dataset for all the remaining questions.**

- **Repartition it to 24 partitions and save it to parquet.**

## What's the size of the folder with results (in MB)?

First, I downloaded the dataset using bash.

```bash
!wget https://nyc-tlc.s3.amazonaws.com/trip+data/fhvhv_tripdata_2021-02.csv
```
Afterwards, I read the CSV file with spark.
```python
df_fhv = spark.read \
    .option("header","true") \
    .csv("fhvhv_tripdata_2021-02.csv")
```
Next, I read the CSV file with Pandas and output the data types of each column.

```python
df_fhv_pandas = pd.read_csv("fhvhv_tripdata_2021-02.csv")

print(df_fhv_pandas.dtypes)
```
Afterwards, I created a dataframe with spark using the pandas dataframe and output the schema of the new spark dataframe. I did this so that I could compare the schema from the spark dataframe and the pandas dataframe.

```python
print(spark.createDataFrame(df_fhv_pandas).schema)
```

Since the pandas dataframe was more accurate, I modified the spark dataframe schema so that it had the same datatypes from the pandas dataframe and created a mock schema.

```python
schema = types.StructType([
    types.StructField("hvfhs_license_num",types.StringType(),True),
    types.StructField("dispatching_base_num",types.StringType(),True),
    types.StructField("pickup_datetime",types.TimestampType(),True),
    types.StructField("dropoff_datetime",types.TimestampType(),True),
    types.StructField("PULocationID",types.IntegerType(),True),
    types.StructField("DOLocationID",types.IntegerType(),True),
    types.StructField("SR_Flag",types.StringType(),True)
]) 
```
The next step was to read the CSV file with spark again but now with the schema I previously made.

```python
df_fhv = spark.read \
    .option("header","true") \
    .schema(schema) \
    .csv("fhvhv_tripdata_2021-02.csv")
```
After, I repartition the file in 24 parts.
```python
df_fhv.repartition(24)
```
At the end I converted the 24 partition files into parquet files and saved them.
```python 
df_fhv.write.parquet("fhvhv/2021/02")
```
To check total size of the parquet files I went to the respective folder ("fhvhv/2021/02") using bash. Next, I checked the size of the parquet files using the command ```ls -lh``` and the total size of all the parquet files was ```147M```. My Answer was not among the possible options but I selected the closest one which was 158MB

## Question 3. Count records, How many taxi trips were there on February 15?

- **Consider only trips that started on February 15.**

First, I opened all the parquet partition files.

```python
df_hfhv = spark.read.parquet('fhvhv/2021/02/*')
```

Next, I created temporal table from the data of the parquet file so that I could do SQL queries on the data.

```python
df_hfhv.registerTempTable('hfhv_trips_data')
```
Afterwards, I used a SQL to query to answer the question.

```python
df_result = spark.sql("""
SELECT 
    COUNT(pickup_datetime) AS frequency
FROM hfhv_trips_data
WHERE 
    pickup_datetime >= '2021-02-15 00:00:00' AND
    pickup_datetime < '2021-02-16 00:00:00'
""").show()
```
My output:

|frequency|
-----------
|   367170|

**There were 367,170 trips in February of 2021.**

## Question 4. Longest trip for each day

- **Now calculate the duration for each trip.**

## Which day had the longest trip?

Using the infrastructure from the previous question I just did another SQL query to answer the question. 

I calculated the difference of hours between each dropoff and pickup datetime.

```python
df_result = spark.sql("""
SELECT 
    pickup_datetime,
    dropoff_datetime,
    EXTRACT(HOUR FROM dropoff_datetime - pickup_datetime) AS HourDifference
FROM hfhv_trips_data
ORDER BY HourDifference DESC
LIMIT 5
""").show()
```
My output:

| pickup_datetime     | dropoff_datetime    | HourDifference |
|---------------------|---------------------|----------------|
| 2021-02-11 13:40:44 | 2021-02-12 10:39:44 | 20             |
| 2021-02-17 15:54:53 | 2021-02-18 07:48:34 | 15             |
| 2021-02-20 12:08:15 | 2021-02-21 00:22:14 | 12             |
| 2021-02-03 20:24:25 | 2021-02-04 07:41:58 | 11             |
| 2021-02-19 23:17:44 | 2021-02-20 09:44:01 | 10             |

**The day with the longest trip was the 11th of February.**

## Question 5. Most frequent dispatching_base_num

- **Now find the most frequently occurring dispatching_base_num in this dataset.**

## How many stages does the spark job has?

Using the same infrastructure from question 3. I wrote a SQL query to find out the most frequent ```dispatching_base_num```. 

```python
df_result = spark.sql("""
SELECT 
    dispatching_base_num,
    COUNT(dispatching_base_num) AS frequency
FROM hfhv_trips_data
GROUP BY dispatching_base_num
ORDER BY frequency DESC
LIMIT 5
""").show()
```
My output:

| dispatching_base_num | frequency |
|----------------------|-----------|
| B02510               | 3233664   |
| B02764               | 965568    |
| B02872               | 882689    |
| B02875               | 685390    |
| B02765               | 559768    |

**The most frequent ``dispatching_base_num`` was B02510.**

**Additionally, the spark job had 3 stages.**

## Question 6. Most common locations pair

**- Find the most common pickup-dropoff pair.**

**For example:**

**"Jamaica Bay / Clinton East"**

- **Enter two zone names separated by a slash**

- **If any of the zone names are unknown (missing), use "Unknown". For example, "Unknown / Clinton East"**

I used the same infrastructure from question 3 plus the data from the taxi zones.

I turned the data from the taxi zones into a temporal table so that I can use SQL queries on it.

```python
df_zones = spark.read.parquet('zones/*')

df_zones.registerTempTable('taxi_zone')
```
After that, to be able to know the answer to the question. I had to do 2 left joins between ``hfhv_trips_data`` and the ``taxi_zone`` so that I could obtain the names from the pickup and dropoff zone IDs.

The CONCAT function allowed me to put the pickup and the dropoff zone names in one single column. 

The COALESCE function helped me rename the NULL values to Unknown.

```python
df_result = spark.sql("""
SELECT
    CONCAT(
        COALESCE(pickup_zone.zone, 'Unknown'),
        ' / ',
        COALESCE(dropoff_zone.zone, 'Unknown')
    ) AS location_pair,
    COUNT(pickup_zone.zone) AS frequency
FROM hfhv_trips_data
LEFT JOIN taxi_zone AS pickup_zone
    ON hfhv_trips_data.pulocationid = pickup_zone.locationid 
LEFT JOIN taxi_zone AS dropoff_zone
    ON hfhv_trips_data.dolocationid = dropoff_zone.locationid
GROUP BY
    pickup_zone.zone,
    dropoff_zone.zone
ORDER BY frequency DESC
LIMIT 10
""").show()
```
My output:

| location_pair                               | frequency |
|---------------------------------------------|-----------|
| East New York / East New York               | 45041     |
| Borough Park / Borough Park                 | 37329     |
| Canarsie / Canarsie                         | 28026     |
| Crown Heights North / Crown Heights North   | 25976     |
| Bay Ridge / Bay Ridge                       | 17934     |
| Jackson Heights / Jackson Heights           | 14688     |
| Astoria / Astoria                           | 14688     |
| Central Harlem North / Central Harlem North | 14481     |
| Bushwick South / Bushwick South             | 14424     |
| Flatbush/Ditmas Park / Flatbush/Ditmas Park | 13976     |

**The most common pickup-dropoff pair was East New York / East New York with a frequency of 45,041 times.**

**Additionally, the spark job performed 3 stages.**


</details>