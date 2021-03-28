# OVERVIEW

This is Udacity's first project under Programming for Data Science Nanodegree Program. This project was focused on exploring a relational database using SQL. (Created on September 10, 2018)

This project focuses on gaining on hand experience with exploring a relational database. This project uses SQL and data visualization to explore the Sakila Movie Database. As part of this project, we are to ask interesting questions about the data and create and run SQL queries to answer these questions.

**This project includes:**
+ A [set of slides](report.pdf) with a question, visualization, and small summary on each slide.
+ A [text file](queries.txt)  with my queries needed to answer each of the four questions.

# ABOUT THE DATASET

The Sakila Movie Database is a fictional database that represents the business process of a DVD rental store. The sample database contains 15 tables and can be found [here.](http://www.postgresqltutorial.com/postgresql-sample-database/)

[**DVD RENTAL ER MODEL**](dvd-rental-erd.pptx)

<img width="387" alt="tables" src="https://user-images.githubusercontent.com/63715337/112750544-d55faf80-8fe6-11eb-895e-2dc0bcf1e7f4.png">



<center> _  _  _  _  _  _  _  _  _  __  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _    _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  _  </center>

### Question 1:
We want to understand more about the movies that families are watching. The following categories are considered family movies: Animation, Children,
Classics, Comedy, Family and Music.
Create a query that lists each movie, the film category it is classified in, and the number of times it has been rented out.

**Query1-query used for first insight**

<p>SELECT f.title film_title,c.name category_name,COUNT(*) rental_count <br>
FROM category c <br>
JOIN film_category fc <br>
ON c.category_id=fc.category_id <br>
JOIN film f <br>
ON f.film_id=fc.film_id <br>
JOIN inventory i <br>
ON i.film_id=f.film_id <br>
JOIN rental r <br>
ON r.inventory_id=i.inventory_id <br>
WHERE c.name IN ('Animation','Children','Classics','Comedy','Family','Music') <br>
GROUP BY 1,2 <br>
Order by 2; </p>

 _ _ _ _

### Question 2:
Now we need to know how the length of rental duration of these family-friendly movies compares to the duration that all movies are rented for.
Can you provide a table with the movie titles and divide them into 4 levels (first_quarter, second_quarter, third_quarter, and final_quarter) based on 
the quartiles (25%, 50%, 75%) of the rental duration for movies across all categories? Make sure to also indicate the category that these 
family-friendly movies fall in

**Query2-query used for second insight**

<p>SELECT f.title,c.name,f.rental_duration,NTILE(4) OVER(ORDER BY f.rental_duration) standard_quartile <br>
FROM category c  <br>
JOIN film_category fc <br>
ON c.category_id=fc.category_id <br>
JOIN film f <br>
ON f.film_id=fc.film_id <br>
WHERE c.name IN ('Animation','Children','Classics','Comedy','Family','Music');</p>

 _ _ _ _

### Question 3:
Finally, provide a table with the family-friendly film category, each of the quartiles, and the corresponding count of movies within each combination 
of film category for each corresponding rental duration category. The resulting table should have three columns:
1)Category
2)Rental length category
3)Count

**Query3-query used for third insight**

<p>SELECT t1.name,t1.standard_quartile, <br>
COUNT(standard_quartile) <br>
FROM <br>
(SELECT c.name,NTILE(4) OVER(ORDER BY f.rental_duration) standard_quartile <br>
FROM category c <br>
JOIN film_category fc <br>
ON c.category_id=fc.category_id <br>
JOIN film f <br>
ON f.film_id=fc.film_id <br>
WHERE c.name IN ('Animation','Children','Classics','Comedy','Family','Music'))t1 <br>
GROUP BY 1,2 <br>
Order by 1,2;</p>

 _ _ _ _
 
### Question 4:
We would like to know who were our top 10 paying customers, how many payments they made on a monthly basis during 2007, and what was the amount of the monthly payments. Can you write a 
query to capture the customer name, month and year of payment, and total payment amount for each month by these top 10 paying customers?
Direction for query formation: The results are sorted first by customer name and then for each month. Also, total amounts per month will be listed for each customer.

**Query 4-query used for fourth insight**

<p>SELECT DATE_TRUNC('month', p.payment_date) pay_month, c.first_name || ' ' || c.last_name AS full_name, COUNT(p.amount) AS pay_countpermon, SUM(p.amount) AS pay_amount <br>
FROM customer c <br>
JOIN payment p <br>
ON p.customer_id = c.customer_id <br>
WHERE c.first_name || ' ' || c.last_name IN <br>
(SELECT t1.full_name <br>
FROM <br>
(SELECT c.first_name || ' ' || c.last_name AS full_name, SUM(p.amount) as amount_total <br>
FROM customer c <br>
JOIN payment p <br>
ON p.customer_id = c.customer_id <br>
GROUP BY 1	<br>
ORDER BY 2 DESC <br>
LIMIT 10) t1) AND (p.payment_date BETWEEN '2007-01-01' AND '2008-01-01') <br>
GROUP BY 2, 1 <br>
ORDER BY 2, 1, 3;</p>
