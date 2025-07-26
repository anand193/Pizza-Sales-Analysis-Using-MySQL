# Pizza-Sales-Analysis-Using-MySQL
Pizza Sales Analysis using MySQL — uncovering key metrics like revenue, top products, and peak hours.

## 🎯 Business Objective

The pizza store's management wanted answers to key questions such as:

- Which pizzas are the best and worst sellers?
- What are the peak order hours and days?
- How much revenue is generated each month?
- Which pizza size generates the most revenue?
- What's the average sales per day?

Answering these questions can help in optimizing inventory, marketing, and staffing strategies.

## 🛠 Tools & Technologies

- MySQL 8.0
- MySQL Workbench
- SQL (Joins, Aggregations, Group By, Subqueries, etc.)
- Git & GitHub

- ## 📂 Dataset

The dataset consists of four main tables:

- oders: Contains order dates and times
- order_details: Contains quantity and pizza IDs per order
- pizzas: Contains pizzas sizes, and prices
- pizza_types: Contains name, category and ingredients

The dataset covers multiple months of pizza orders.

## 🔍 Key SQL Questions & Analysis

Here are some of the queries I used to generate insights:

### 1. Total Revenue
```sql
select * from pizzas;
select round(sum(order_details.quantity*pizzas.price),2) as total_revenue
from order_details
join pizzas 
on order_details.pizza_id = pizzas.pizza_id;
```
### 2. highest-priced pizza
```sql
SELECT  t.name, p.price
FROM pizza_types t
JOIN pizzas p
ON p.pizza_type_id = t.pizza_type_id
ORDER BY price DESC
LIMIT 1;

### 3. most common pizza size ordered
```sql
SELECT pizzas.size,COUNT(order_details.order_details_id) AS order_count
FROM pizzas
JOIN order_details 
ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizzas.size
ORDER BY order_count DESC
LIMIT 1;

### 4. top 5 most ordered pizza types along with their quantities
```sql
SELECT ps.name, sum(o.quantity) AS Quantities
FROM order_details o
JOIN pizzas p 
ON p.pizza_id = o.pizza_id
JOIN pizza_types ps 
ON ps.pizza_type_id = p.pizza_type_id
GROUP BY  ps.name
ORDER BY Quantities DESC
LIMIT 5 ;

### 5. total quantity of each pizza category ordered
```sql
select pizza_types.category,
sum(order_details.quantity) as quantity
from pizza_types join pizzas
on pizza_types.pizza_type_id = pizzas.pizza_type_id
join order_details
on order_details.pizza_id = pizzas.pizza_id
group by pizza_types.category order by quantity desc;

### 6.  average number of pizzas ordered per day
```sql
select round(avg(quantity),0) as avg_pizza_ordered_per_day from 
(SELECT orders.order_date, SUM(order_details.quantity) as quantity
FROM orders
JOIN order_details
ON orders.order_id = order_details.order_id
GROUP BY orders.order_date
ORDER BY orders.order_date) as order_quantity;

### 7. distribution of orders by hour of the day
```sql
SELECT HOUR(order_time) AS hour, COUNT(order_id) AS order_count
FROM orders
GROUP BY HOUR(order_time);

### 8. top 3 most ordered pizza types based on revenue
```sql
SELECT pizza_types.name,SUM(order_details.quantity * pizzas.price) AS revenue
FROM pizza_types
JOIN pizzas 
ON pizzas.pizza_type_id = pizza_types.pizza_type_id
JOIN order_details
ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY revenue DESC
LIMIT 3;

### 9. percentage contribution of each pizza type to total revenue
```sql
SELECT pizza_types.category,ROUND(SUM(order_details.quantity * pizzas.price) / (SELECT 
ROUND(SUM(order_details.quantity * pizzas.price),2) AS total_revenue_generated
FROM order_details
JOIN pizzas 
ON order_details.pizza_id = pizzas.pizza_id) * 100,2) AS revenue
FROM pizza_types
JOIN pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY revenue DESC;

### 10. cumulative revenue generated over time.
```sql
select order_date,sum(revenue)over(order by order_date) as cum_revenue
from (select orders.order_date,sum(order_details.quantity*pizzas.price)  as revenue
from order_details
join pizzas 
on pizzas.pizza_id   = order_details.pizza_id
join orders
on orders.order_id = order_details.order_id
group by orders.order_date) as sales;

### 11. top 3 most ordered pizza types based on revenue for each pizza category.
```sql
select name,revenue from 
(select category,name,revenue,
rank()over(partition by category order by revenue desc) as rn
from
(select pizza_types.category,pizza_types.name,
sum((order_details.quantity)*pizzas.price) as revenue
from pizza_types join pizzas
on pizza_types.pizza_type_id = pizzas.pizza_type_id
join order_details
on order_details.pizza_id = pizzas.pizza_id
group by pizza_types.category,pizza_types.name) as a) as b
where rn<=3;


## 📈 Key Insights from SQL Analysis

- 💰 **Total revenue** generated during the analysis period was **$817,860**.
  > *Query 1: Total Revenue*

- 🏆 The **highest-priced pizza** was **"The Greek Pizza"** priced at **35.95**.
  > *Query 2: Highest-Priced Pizza*

- 🍕 The most commonly ordered **pizza size** was **Large**, with **18526 orders**, indicating customer preference for bigger portions.
  > *Query 3: Most Common Pizza Size*

- 🔥 The **top 3 most revenue-generating pizza types** were:
  1. **The Thai Chicken Pizza** — 43434.25
  2. **The Barbecue Chicken Pizza** — 42768 
  3. **'The California Chicken Pizza** — 41409.5 
  > *Query 8: Pizza Types by Revenue*

- 🏷️ The **top pizza category by total quantity sold** was **Classic**, with **14,888 pizzas** ordered.
  > *Query 5: Total Quantity by Category*

- ⏰ Most orders were placed around **12 PM**, making it the peak hour for business.
  > *Query 7: Distribution of Orders by Hour*

- 📈 **Cumulative revenue** shows a steady upward trend, with spikes around weekends and end-of-month periods.
  > *Query 10: Cumulative Revenue Over Time*

- 📊 **Pizza categories contributing most to revenue**:
  - **Classic**: 26.91%
  - **Chicken**: 23.96%
  - **Veggie**: 23.96%
  - **Supreme**: 25.46%
  > *Query 9: Revenue Contribution by Category*

- 📅 On average, the store sells about **138 pizzas per day**.
  > *Query 6: Average Daily Pizza Sales*

## 🧾 Conclusion

This analysis uncovered key insights to optimize pizza sales operations:

- Focus on top-selling pizza types and medium sizes to boost revenue.
- Leverage hourly order trends to plan staff shifts and targeted promotions.
- Monitor category-wise revenue to align product offerings with demand.

With minimal SQL, we revealed impactful trends that can directly influence marketing, inventory, and pricing strategies.

## 👤 Author

- **Anand Mehto**
- [LinkedIn](linkedin.com/in/anand-mehto-72a11618b)
