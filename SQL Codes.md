# SQL Codes 

Here are some SQL codes to keep in mind

One can use `COUNT()` inside a `HAVING` without having the `COUNT()` at the beginning. The question was, 

*Get the country, average budget, and average gross take of countries that have made more than 10 films. Order the result by country name, and limit the number of results displayed to 5. You should alias the averages as `avg_budget` and `avg_gross` respectively.*

```sql
SELECT country, AVG(budget) AS avg_budget, AVG(gross) AS avg_gross
FROM films
GROUP BY country
HAVING COUNT(country) > 10
ORDER BY country
LIMIT 5
```

