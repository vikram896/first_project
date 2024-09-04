---
title: Movie Rental Store Dashborad 🎥🍿🎬
---


```sql year
select extract('year' from random_date) AS order_year
from payment
group by 1
```
```sql genres
select name as genres from category
group by 1
```


```sql Trend_analysis
SELECT 
    DATE_TRUNC('month', random_date) AS order_month,
    ROUND(SUM(amount), 0) AS sales
FROM 
    category c
    JOIN film_category fc ON c.category_id = fc.category_id
    JOIN film f ON fc.film_id = f.film_id
    JOIN inventory i ON f.film_id = i.film_id
    JOIN rental r ON i.inventory_id = r.inventory_id
    JOIN payment p ON r.rental_id = p.rental_id
    wHERE extract('year' from random_date) = '${inputs.year.value}' and name = '${inputs.genres.value}'
GROUP BY 1
ORDER BY 1 
```



```SQL customer_base
select c.name AS Genre, count(cus.customer_id) as cus_base
from  category c
join film_category fc
using(category_id)
join film f
using(film_id)
join inventory i
using(film_id)
join rental re
using(inventory_id)
join customer cus
using(customer_id)
join payment pay
using(customer_id)
WHERE extract('year' from random_date) = '${inputs.year.value}'
group by 1
order by 2 desc;
```

```sql Total_sales_for_geners
SELECT c.name AS Genre, SUM(p.amount) AS total_sales
            FROM category c
            JOIN film_category fc
            USING(category_id)
            JOIN film f
            USING(film_id)
            JOIN inventory i
            USING(film_id)
            JOIN rental r
            USING(inventory_id)
            JOIN payment p
            USING(rental_id)
            WHERE extract('year' from random_date) = '${inputs.year.value}' 
            GROUP BY 1
            ORDER BY 2 DESC
```

```sql customer_base_in_Top10_country
SELECT 
    cou.country AS COUNTRY,
    COUNT(DISTINCT ca.customer_id) AS distinct_customers,
    SUM(p.amount) AS total_sales
FROM 
    category c
    JOIN film_category fc ON c.category_id = fc.category_id
    JOIN film f ON fc.film_id = f.film_id
    JOIN inventory i ON f.film_id = i.film_id
    JOIN rental r ON i.inventory_id = r.inventory_id
    JOIN payment p ON r.rental_id = p.rental_id
    JOIN customer ca ON p.customer_id = ca.customer_id
    JOIN address ad ON ca.address_id = ad.address_id
    JOIN city cit ON ad.city_id = cit.city_id
    JOIN country cou ON cit.country_id = cou.country_id
    WHERE extract('year' from random_date) = '${inputs.year.value}' and name = '${inputs.genres.value}'
GROUP BY 
     cou.country
ORDER BY
    total_sales DESC
limit 10;
```




<Dropdown 
    data={year} 
    name=year 
    value=order_year 
    title=Year
/>

<Dropdown 
    data={genres} 
    name=genres 
    value=genres 
    title=Genres
/>

<Grid cols=2>
<LineChart 
    data={customer_base}
    x=Genre
    y=cus_base
    yFmt=num1k 
    title="Customer Base For Genre's"
    markers=true
    labels=true
    labelPosition=below
/>

<BarChart 
    data={Total_sales_for_geners}
    x=Genre
    y=total_sales 
    yFmt=usd0k
    title="Total sales for genre"
    labels=true
    showAllLabels=true
/>
</Grid>

<Grid cols=2>
<BarChart 
    data={customer_base_in_Top10_country} 
    x=COUNTRY 
    y=total_sales
    yFmt=usd
    title='Customer Base In Top 10 Countries Along With Sales'
    y2=distinct_customers
    y2SeriesType=line
    labels=true
    
/>

<AreaChart
    title='Monthly sales, Last 12 months'
    subtitle='USD'
    data={Trend_analysis}
    x=order_month
    y=sales
    yFmt=usd
    labels=true
/>
</Grid>

# Project Done By @https://github.com/vikram896git 