# SQL for Movies

## Tables

**Customer Table**

|customer_id|name|country|gender|date_of_birth|date_account_start|

**Movies Table**

|movie_id|title|genre|runtime|year_of_releas|renting_price|

**Renting**

|renting_id|customer_id|movie_id|rating|date_renting|

**actors**

|actor_id|name|year_of_birth|nationality|gender|

**actsin**

|actsin_id|movie_id|actor_id|



## Questions

1. List the total number of rented movies, the number of ratings, and their average rating for the year 2019.
2. Find the average rating per customer, the number of ratings per customer, number of movie rentals per customer using the renting table. 
3. Augment the table `renting` with all columns from the table `customers` with a `LEFT JOIN` and select only customers frm Belgium
4. Calculate the revenue coming from movie rentals, the number of movie rentals and the number of customers who rented a movie. Only return the rentals from 2018.
5. Create a list of actor names and movie titles in which they act. Make sure that each combination of actor and movie appears only once.
6. Report year of birth for the oldest and yougest actress in each country
7. How much money did each customer spent?
8. How much income did each movie generate? Do this with and without using a subquery.
9. Using the previous query, rank the movies in terms of total income generated and return the movies ranked 10-13. 
10. Report the date of birth of the oldest and youngest US actor and actress
11. Who is the favorite male actor for a certain customer group? Define by most watched or by average rating. 
12. Identify favorite movies for a group of Customers that are born in 1970s. 
13. Identify favorite actor for Spain. Do this by their name and gender. 
14. Provide the total number of movie rentals, the average rating of all movies and the total revenue for each country since the beginning of 2019. 











## Solutions

1. ```sql
   SELECT 
   	COUNT(*) AS number_renting,
   	AVG(rating) AS average_rating, 
       COUNT(rating) AS number_ratings -- Add the total number of ratings here.
   FROM renting
   WHERE date_renting >= '2019-01-01';
   ```

2. ```sql
   SELECT customer_id, -- Report the customer_id
         AVG(rating),  -- Report the average rating per customer
         COUNT(customer_id),  -- Report the number of ratings per customer
         COUNT(*)  -- Report the number of movie rentals per customer
   FROM renting
   GROUP BY customer_id
   HAVING COUNT(*) > 7 -- Select only customers with more than 7 movie rentals
   ORDER BY AVG(rating) DESC; -- Order by the average rating in ascending order
   ```

3. ```sql
   SELECT *
       FROM renting AS r
       LEFT JOIN customers AS c
       ON r.customer_id = c.customer_id
       WHERE c.country='Belgium';
   ```

4. ```sql
   SELECT 
   	SUM(m.renting_price), -- Get the revenue from movie rentals
   	COUNT(*), -- Count the number of rentals
   	COUNT(DISTINCT r.customer_id) -- Count the number of customers
   FROM renting AS r
   LEFT JOIN movies AS m
   ON r.movie_id = m.movie_id
   WHERE date_renting BETWEEN '2018-01-01' AND '2018-12-31';
   ```

5. ```sql
   SELECT *
       FROM actors AS a
       LEFT JOIN actsin AS ai
       ON a.actor_id = ai.actor_id
       LEFT JOIN movies AS m
       ON ai.movie_id = m.movie_id
   ```

6. ```sql
   SELECT af.nationality,
   		MAX(af.year_of_birth) AS youngest,
          	MIN(af.year_of_birth) AS oldest
   FROM (SELECT *
   		FROM actors
   		WHERE gender = 'female') AS af
   GROUP BY af.nationality
   ORDER BY af.nationality 
   ```

7. ```sql
   SELECT c.customer_id AS customer,
   	   SUM(m.renting_price) AS price
   	FROM customers AS c
   	LEFT JOIN renting AS r
   	ON c.customer_id = r.customer_id
   	LEFT JOIN movies AS m
   	ON r.movie_id = m.movie_id
   	GROUP BY c.customer_id
   	HAVING  SUM(m.renting_price) IS NOT NULL
   	ORDER BY price DESC;
   ```

8. ```sql
   SELECT rm.title, -- Report the income from movie rentals for each movie 
          SUM(rm.renting_price) AS income_movie
   FROM
          (SELECT m.title,  
                  m.renting_price
          FROM renting AS r
          LEFT JOIN movies AS m
          ON r.movie_id=m.movie_id) AS rm
   GROUP BY rm.title
   ORDER BY income_movie DESC; -- Order the result by decreasing income
   ```

9. ```sql
   
   ```

10. ```sql
    SELECT a.gender, -- Report for male and female actors from the USA 
           MIN(a.year_of_birth), -- The year of birth of the oldest actor
           MAX(a.year_of_birth) -- The year of birth of the youngest actor
    FROM
        (SELECT * -- Use a subsequent SELECT to get all information about actors from the USA
        FROM actors
        WHERE nationality = 'USA') AS a -- Give the table the name a
    GROUP BY a.gender;
    ```

11. ```sql
    SELECT a.name
    	COUNT(*) AS number_views,
    	AVG(r.rating) AS avg_rating
    FROM renting AS r
    LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id
    LEFT JOIN actsin AS ai
    ON r.movie_id = ai.movie_id
    LEFT JOIN actors as a
    ON a.actor_id = a.actor_id
    
    WHERE c.gender = 'male'
    GROUP BY a.name
    HAVING AVG(r.rating) IS NOT NULL
    ORDER BY avg_rating DESC, number_views DESC;
    ```

12. ```sql
    SELECT m.title, 
    COUNT(*),
    AVG(r.rating)
    FROM renting AS r
    LEFT JOIN customers AS c
    ON c.customer_id = r.customer_id
    LEFT JOIN movies AS m
    ON m.movie_id = r.movie_id
    WHERE c.date_of_birth BETWEEN '1970-01-01' AND '1979-12-31'
    GROUP BY m.title
    ```

13. ```sql
    SELECT a.name,  c.gender,
           COUNT(*) AS number_views, 
           AVG(r.rating) AS avg_rating
    FROM renting as r
    LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id
    LEFT JOIN actsin as ai
    ON r.movie_id = ai.movie_id
    LEFT JOIN actors as a
    ON ai.actor_id = a.actor_id
    WHERE c.country = 'Spain' -- Select only customers from Spain
    GROUP BY a.name, c.gender
    HAVING AVG(r.rating) IS NOT NULL 
      AND COUNT(*) > 5 
    ORDER BY avg_rating DESC, number_views DESC;
    ```

14. ```sql
    SELECT 
    	c.country,                    -- For each country report
    	COUNT(*) AS number_renting, -- The number of movie rentals
    	AVG(r.rating) AS average_rating, -- The average rating
    	SUM(renting_price) AS revenue         -- The revenue from movie rentals
    FROM renting AS r
    LEFT JOIN customers AS c
    ON c.customer_id = r.customer_id
    LEFT JOIN movies AS m
    ON m.movie_id = r.movie_id
    WHERE date_renting >= '2019-01-01'
    GROUP BY c.country;
    ```

15. 

