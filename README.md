# MarketingAnalyticsCaseStudy_SQL
To help marketing team in their first email campaign by providing necessary insights using advance SQL queries.

**Problem Statement:**

Generate the analytical inputs required to drive DVD Rental Co marketing team's very first customer email campaign.
The marketing team expects their personalised emails to drive increased sales and engagement from the DVD Rental Co customer base.

The main initiative is to share insights about each customer’s viewing behaviour to demonstrate DVD Rental Co’s relentless focus on customer experience.

The insights requested by the marketing team include key statistics about each customer’s top 2 categories and favourite actor. 
There are also 3 personalised recommendations based off each customer’s previous viewing history as well as titles which are popular with other customers.

1. Email template
  The email template has been provided and consists of data analytics and customer insight components numbered.
2. Category Insights

Top Category
> What was the top category watched by total rental count?
> How many total films have they watched in their top category and how does it compare to the DVD Rental Co customer base?
> How many more films has the customer watched compared to the average DVD Rental Co customer?
> How does the customer rank in terms of the top X% compared to all other customers in this film category?
> What are the top 3 film recommendations in the top category ranked by total customer rental count which the customer has not seen before?

Second Category
> What is the second ranking category by total rental count?
> What proportion of each customer’s total films watched does this count make?
> What are top 3 recommendations for the second category which the customer has not yet seen before?

3. Actor Insights
> Which actor has featured in the customer’s rental history the most?
> How many films featuring this actor has been watched by the customer?
> What are the top 3 recommendations featuring this same actor which have not been watched by the customer?

4. Entity Relationship Diagram
Provided with the entity relationship diagram (ERD) as shown below with all foreign table keys and column data types:

![image](https://user-images.githubusercontent.com/89623051/137928027-8871319e-3d51-4d06-9ea5-aaba3777b241.png)


**Exploration
**
We have a total of 7 tables in our ERD for this case study highlighting the important columns which we should use to join our tables for our data analysis task.

Table Investigation

We analysed the relationships between the most important columns from each table to determine if there were one-many, many-one or many-to-many relationships to further guide our table joining investigation.

Join Column Analysis

Explored the different join column values to check the coverage of join columns and also identify any duplicates in joining column values.
Our analysis informed our choice of SQL table join method. In our examples - most of the join column values have a 100% overlap with no duplicate column values
so we have decided to proceed with exclusively INNER JOIN for our SQL scripts. We also confirmed that there was no difference in the row counts by comparing
LEFT JOIN and INNER JOIN results with our previous queries.

Here is a sample of the analysis we completed for our exploration of the dvd_rentals.rental and dvd_rentals.inventory tables:
Perform an anti join to check which column values exist in dvd_rentals.rental but not in dvd_rentals.inventory

-- how many foreign keys only exist in the left table and not in the right?

SELECT
  COUNT(DISTINCT rental.inventory_id)
FROM dvd_rentals.rental
WHERE NOT EXISTS (
  SELECT inventory_id
  FROM dvd_rentals.inventory
  WHERE rental.inventory_id = inventory.inventory_id
);

checked the right side table using the same process: dvd_rentals.inventory

-- how many foreign keys only exist in the right table and not in the left?
-- note the table reference changes
SELECT
  COUNT(DISTINCT inventory.inventory_id)
FROM dvd_rentals.inventory
WHERE NOT EXISTS (
  SELECT inventory_id
  FROM dvd_rentals.rental
  WHERE rental.inventory_id = inventory.inventory_id
);

There seems to be a single value which is not showing up - let’s investigate which film it is:

SELECT *
FROM dvd_rentals.inventory
WHERE NOT EXISTS (
  SELECT inventory_id
  FROM dvd_rentals.rental
  WHERE rental.inventory_id = inventory.inventory_id
);

Conclusion: 

Although there is a single inventory_id record which is missing from the dvd_rentals.rental table - there might be no issues with this discrepancy 
as it seems that some inventory might just never be rented out to customers at the retail rental stores.

Finally - 
let’s confirm that both left and inner joins do not differ at all when we look at the resulting row counts from the joint tables:

DROP TABLE IF EXISTS left_rental_join;
CREATE TEMP TABLE left_rental_join AS
SELECT
  rental.customer_id,
  rental.inventory_id,
  inventory.film_id
FROM dvd_rentals.rental
LEFT JOIN dvd_rentals.inventory
  ON rental.inventory_id = inventory.inventory_id;

DROP TABLE IF EXISTS inner_rental_join;
CREATE TEMP TABLE inner_rental_join AS
SELECT
  rental.customer_id,
  rental.inventory_id,
  inventory.film_id
FROM dvd_rentals.rental
INNER JOIN dvd_rentals.inventory
  ON rental.inventory_id = inventory.inventory_id;

-- Output SQL
(
  SELECT
    'left join' AS join_type,
    COUNT(*) AS record_count,
    COUNT(DISTINCT inventory_id) AS unique_key_values
  FROM left_rental_join
)
UNION
(
  SELECT
    'inner join' AS join_type,
    COUNT(*) AS record_count,
    COUNT(DISTINCT inventory_id) AS unique_key_values
  FROM inner_rental_join
);

Performed this same analysis for all of our tables within our core tables and concluded that the distribution for each of the join keys are as expected 
and are similar to what we see for these first 2 tables.




  
