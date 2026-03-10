Music_Database SQL Project
1.	Objective
This project is for beginners and will teach you how to analyze the music playlist database. You can examine the dataset with SQL and help the store understand its business growth by answering simple questions.

2.	Database and Tools 
- PostgreSQL
- pgAdmin

Schema- Music Store Database
 

3. Project Overview
This project analyzes a Music Store Database using SQL queries.
The goal is to explore business insights such as employee hierarchy, customer distribution by country, and invoice totals.
3.	SQL Queries and 
Q1: Who is the senior most employee based on job title?
SELECT title, last_name, first_name 
FROM employee
ORDER BY levels DESC
LIMIT 1
 
 Q2: What are top 3 values of total invoice? */

SELECT total 
FROM invoice
ORDER BY total DESC
 
Q3: Who is the best customer? The customer who has spent the most money will be declared the best customer. 
Write a query that returns the person who has spent the most money.*/

SELECT customer.customer_id, first_name, last_name, SUM(total) AS total_spending
FROM customer
JOIN invoice ON customer.customer_id = invoice.customer_id
GROUP BY customer.customer_id
ORDER BY total_spending DESC
LIMIT 1; 
Q4: Return all the track names that have a song length longer than the average song length. 
Return the Name and Milliseconds for each track. Order by the song length with the longest songs listed first. */

SELECT name,miliseconds
FROM track
WHERE miliseconds > (
	SELECT AVG(miliseconds) AS avg_track_length
	FROM track )
ORDER BY miliseconds DESC;

 
Q5: We want to find out the most popular music Genre for each country. We determine the most popular genre as the genre 
with the highest amount of purchases. Write a query that returns each country along with the top Genre. For countries where 
the maximum number of purchases is shared return all Genres. */

WITH popular_genre AS 
(
    SELECT COUNT(invoice_line.quantity) AS purchases, customer.country, genre.name, genre.genre_id, 
	ROW_NUMBER() OVER(PARTITION BY customer.country ORDER BY COUNT(invoice_line.quantity) DESC) AS RowNo 
    FROM invoice_line 
	JOIN invoice ON invoice.invoice_id = invoice_line.invoice_id
	JOIN customer ON customer.customer_id = invoice.customer_id
	JOIN track ON track.track_id = invoice_line.track_id
	JOIN genre ON genre.genre_id = track.genre_id
	GROUP BY 2,3,4
	ORDER BY 2 ASC, 1 DESC
)
SELECT * FROM popular_genre WHERE RowNo <= 1
 
Q6: Write a query that determines the customer that has spent the most on music for each country. 
Write a query that returns the country along with the top customer and how much they spent. 
For countries where the top amount spent is shared, provide all customers who spent this amount. */

WITH RECURSIVE 
	customter_with_country AS (
		SELECT customer.customer_id,first_name,last_name,billing_country,SUM(total) AS total_spending
		FROM invoice
		JOIN customer ON customer.customer_id = invoice.customer_id
		GROUP BY 1,2,3,4
		ORDER BY 2,3 DESC),

	country_max_spending AS(
		SELECT billing_country,MAX(total_spending) AS max_spending
		FROM customter_with_country
		GROUP BY billing_country)

SELECT cc.billing_country, cc.total_spending, cc.first_name, cc.last_name, cc.customer_id
FROM customter_with_country cc
JOIN country_max_spending ms
ON cc.billing_country = ms.billing_country
WHERE cc.total_spending = ms.max_spending
ORDER BY 1;

 

4.Project Purpose
This project demonstrates:
•	SQL querying
•	Data aggregation
•	Sorting and filtering
•	Basic business analysis using SQL
•	Join
5.Business Recommendation:
The company should focus marketing efforts on high-performing countries like the USA and Canada where the most invoices are generated. Identifying high-value customers based on large invoice totals can help create targeted promotions and loyalty programs to increase revenue. Data-driven insights from PostgreSQL analysis can support better business decisions. 📊

Deepak Kumar Tekchandani
SQL | PostgreSQL | Power BI | Data Analytics  

📧 deepakkumartekchandani@gmail.com  
🔗 LinkedIn: www.linkedin.com/in/deepak-kumar-tekchandani-b7200a146
💻 GitHub: https://github.com/DTekchandani1/Music-Database-SQL-Project.git
👉



