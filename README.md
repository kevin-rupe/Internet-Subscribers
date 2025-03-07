# Internet-Subscribers
Project completed in pursuit of Master's of Science in Data Analytics.


## Research Question

To start off this performance assessment, I began with asking a research question that I wanted to be able to answer using both the original database provided in PostgreSQL and the add-on CSV file provided in the shared folder in Labs on Demand. I chose the ‘services’ csv file because I wanted to use the data in the Internet Service column. The research question that I will answer is: 

***<p style="text-align:center;">“Do the richest customers subscribe to the best internet services more than less wealthy customers?”</p>***

## Identifying Data

The data from the original dataset that I will use is from the customer table. I will use the income column to identify different social wealth classes that I will determine using several CASE statements.  A CASE statement in SQL checks whether a condition is met, and then returns a value you choose (Gupta, 2023). I am classifying “poor” to be any person making $20,000 or less, “middle class” to be any person making between $20,000-$60,000, and “rich” to be anyone making more than $60,000. From the add-on CSV file, I will be using the Internet Service column. I will then merge the two table to understand the relationship between wealth and internet services of customers.

## Logical Data Model 

I used PostgreSQL PGAdmin4 to create the logical data model, or entity relationship diagram (ERD). This diagram shows the relationships between all 6 tables. This ERD shows a star schema with the customer table being the main fact table with the other 5 dimension tables each related via a 1:M (one-to-many) relational key (Malik, Goldwasser, Johnston, 44).

![Image 3-7-25 at 11 07 AM](https://github.com/user-attachments/assets/c398157c-efad-4c0c-be22-10a938516598)

## Code for the Physical Data Model 

I wrote the following SQL statement in PGAdmin4 which created the services table. I decided to
update the name of the columns from CamelCase to Snake Case. CamelCase is hard to read in
SQL due to the capitalization of each separate word. Snake Case (also known as underscore case)
is the most preferred way to name schemas, tables, and columns (Martins, 2021). I also used the
customer_id column to be the primary key for this new table. 

```sql
CREATE TABLE services (
  customer_id VARCHAR(7),
  internet_service VARCHAR(20),
  phone VARCHAR(3),
  multiple VARCHAR(3),
  online_security VARCHAR(3),
  online_backup VARCHAR(3),
  device_protection VARCHAR(3),
  tech_support VARCHAR(3),
  PRIMARY KEY (customer_id)
)
```

## Loading CSV Data
 
The following code was copied from PGAdmin4 which can be used in SQL to load the data from
the CSV file being used in this PA. 

```sql
– command “ “\\copy public.services (customer_id, internet_service, phone, multiple,
                                    online_security, online_backup, device_protection, tech_support)
FROM ‘C:/LabFiles/Services.csv’ DELIMETER ‘,’ CSV HEADER QUOTE ‘\’” ESCAPE ‘”;””
```

## SQL Query

I wrote the following SQL query to answer my research question. I am selecting the internet_service column from the services table, and then creating three wealth columns: poor, middle_class, and rich from the income column in the customer table to display how many customers in each of these wealth categories use the best internet service. I am using aliases of my tables to make the code easier to read and understand. I am using an INNER JOIN between the two tables on customer_id, and then sorting the results by internet_service. 

## Add-on File

To determine how often the add-on data should be refreshed in the service table in the churn database, I believe the following factors should be evaluated. First, how often does this store gain new customers? Second, has there been any new internet service options other than DSL or Fiber Optic, which are the only two options provided (other than none)? From a business perspective, I do not believe this data would need to be updated very often. I would recommend an annual update on the service table for the data to remain relevant to answering the business question. 

```sql
SELECT
  s.internet_service,
  COUNT(CASE WHEN income <= '20000' THEN c.customer_id END) AS poor,
  COUNT(CASE WHEN income BETWEEN '20001' AND '60000' THEN c.customer_id END) AS middle_class,
  COUNT(CASE WHEN income >= '60001' THEN c.customer_id END) AS rich
FROM customer c
INNER JOIN services s
  ON c.customer_id = s.customer_id
GROUP BY s.internet_service;
```
|internet_service|poor|middle_class|rich|
|---|---|---|---|
|DSL|901|1845|717|
|Fiber Optic|1158|2439|811|
|None|572|1130|427|



#### Web Sources

Aryan Gupta. (2023, February 27). An Introduction to SQL CASE Statement. Retrieved June 11,
2023, from https://www.simplilearn.com/tutorials/sql-tutorial/sql-case-statement

David Martins. (2021, July 31). 10 Best Practices to Write Readable and Maintainable SQL Code.
Retrieved June 11, 2023, from https://towardsdatascience.com/10-best-practices-to-write-readable-and-maintainable-sqlcode-427f6bb98208

pgAdmin. (n.d.). ERD Tool. Retrieved June 11, 2023, from 
https://www.pgadmin.org/docs/pgadmin4/development/erd_tool.html

#### Sources

Malik, U., Goldwasser, M., & Johnston, B., SQL for Data Analytics: Perform Fast and
Efficient Data Analysis with the Power of SQL (Packt Publishing, Limited., 2019). 


