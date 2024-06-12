# Balaji Fast Food Analysis

![Balaji_fast_food_sales](https://github.com/YuYunusa/Balaji-Fast-Food-Analysis/assets/160647840/fd436bfd-c078-40c2-a303-c6f5f94832ef)

![balaji_fast_food_orders](https://github.com/YuYunusa/Balaji-Fast-Food-Analysis/assets/160647840/d508482f-9276-4f18-9705-f3b269e9e5a3)

## Project Overview
Balaji is a restaurant that offers fast food services to customers. This analysis aims to delve into the restaurant's sales and orders to uncover valuable insights.

## Data Sources
The dataset used in this project is [Balaji Fast Food Sales.csv](https://www.kaggle.com/datasets/rajatsurana979/fast-food-sales-report) and it was gotten from kaggle.The data contains 1000 rows and 10 columns. The columns are as follows:

- order_id: a unique identifier for each order.
  
- date: date of the transaction.
  
- item_name: name of the food.
  
- item_type: category of item (Fastfood or Beverages).
  
- item_price: price of the item for 1 quantity.
  
- Quantity: how much quantity the customer orders.
  
- transaction_amount: the total amount paid by customers.
  
- transaction_type: payment method (cash, online, others).
  
- received_by: gender of the person handling the transaction.
  
- time_of_sale: different times of the day (Morning, Evening, Afternoon, 
  Night, Midnight).

## Tools
- MYSQL: this was used to load, clean, manipulate, and analyze the data

- PowerBI: this was used to visualize the analyzed dataset.

## Data Wrangling
The dataset was carefully cleaned. There were no duplicate values found, but the transaction_type column had missing values, which I replaced with 'Other'.
```SQL
# Replaced the empty rows in the transaction_type column
UPDATE balaji_sales
SET transaction_type = CASE
  WHEN transaction_type LIKE 'Online' THEN 'online'
  WHEN transaction_type LIKE 'Cash' THEN 'cash'
  WHEN transaction_type LIKE '' THEN 'other'
END;
```

The received_by column had it's receiving staffs recorded as 'Mr.' & 'Mrs.' which I changed into 'Male' & 'Female' ('Male Staff' & 'Female Staff' in Power BI while I was creating the visualization).
```SQL
# replaced the content of the received_by column
UPDATE balaji_sales
SET received_by = CASE
  WHEN received_by LIKE 'mr.' THEN 'male'
  WHEN received_by LIKE 'mrs.' THEN 'female'
END;
```
Using the date column, I extracted the name of the month and the day of the week (dow) for further analysis (done differently in Power BI).

## Analysis
1. Total Number of Orders, Revenue Generated, and Average Order Price?
```SQL
SELECT Count(*) total_orders, 
       sum(transaction_amount) total_revenue, 
       round(avg(transaction_amount), 2) average_order_price
FROM balaji_sales;
```

![Screenshot (218)](https://github.com/YuYunusa/Balaji-Fast-Food-Analysis/assets/160647840/49542330-124f-4eaa-b214-a168db5b1952)

A total of 1,000 orders were placed, which generated a revenue of $275,230 and an average order price of $275.23.

2. Total Number of Orders Received by Staff?
```SQL
SELECT received_by, count(received_by) received_by_count
FROM balaji_sales
GROUP BY received_by
ORDER BY received_by_count DESC;
```

![Screenshot (196)](https://github.com/YuYunusa/Balaji-Fast-Food-Analysis/assets/160647840/12439cf4-3c6d-4f6f-a913-e1bf888a625f)

Of the 1,000 orders placed, male staff received 512 orders, while female staff received 488 orders, which is 2.4% less than the male staff.

3. Most Common Method of Payment?
```SQL
SELECT transaction_type, count(transaction_type) transaction_no
FROM balaji_sales
GROUP BY transaction_type
ORDER BY transaction_no desc;
```

![Screenshot (195)](https://github.com/YuYunusa/Balaji-Fast-Food-Analysis/assets/160647840/bada9315-334a-47dd-abf3-f5c73a79c2f5)

Payment with Cash is the most commonly used payment method, followed by Online, with Other being the least used.

5. Most Sold and Ordered Items?
```SQL
SELECT item_name, sum(transaction_amount) item_purchase, count(item_name) item_order
FROM balaji_sales
GROUP BY item_name
ORDER BY item_purchase desc;
```

![Screenshot (199)](https://github.com/YuYunusa/Balaji-Fast-Food-Analysis/assets/160647840/71951b9c-a34e-4c5a-a0c0-826ff8b0ee36)
![Screenshot (200)](https://github.com/YuYunusa/Balaji-Fast-Food-Analysis/assets/160647840/40711eff-ab25-4a50-a39f-34e822e799ef)

Sandwich, Frankie, and Cold Coffee generated more sales than other items accounting for approximately 65% of the total revenue., while Cold Coffee, Sugarcane Juice, and Panipuri were the top 3 most ordered items making up 46% of the total orders.

6. Most Ordered and Sold Item Type?
```SQL
SELECT item_type, sum(transaction_amount) item_type_price, count(item_type) item_type_order
FROM balaji_sales
GROUP BY item_type
ORDER BY item_type desc;
```

![Screenshot (203)](https://github.com/YuYunusa/Balaji-Fast-Food-Analysis/assets/160647840/ce531531-db45-451e-8a31-71834c591d56)
![Screenshot (204)](https://github.com/YuYunusa/Balaji-Fast-Food-Analysis/assets/160647840/3c7e6b6a-38bb-4540-b818-c5737f4a1f55)

Fast Food items outperformed Beverages by 37%, indicating a higher overall interest in fast food.

8. Sales and Orders by Time of Day
```SQL
SELECT time_of_sale, count(time_of_sale) tos_count, sum(transaction_amount) tos_revenue
FROM balaji_sales
GROUP BY time_of_sale
ORDER BY  DESC
```

![Screenshot (207)](https://github.com/YuYunusa/Balaji-Fast-Food-Analysis/assets/160647840/ce1949a5-e65b-4a5e-8882-da3d529a011a)
![Screenshot (208)](https://github.com/YuYunusa/Balaji-Fast-Food-Analysis/assets/160647840/4003d872-2165-4f18-b1f6-77d382275da7)

Night generated the most sales, followed by Afternoon making up approximately 43% of the total revenue. Night and Afternoon also had the most orders making up 41% of the total orders placed, followed closely by Evening and Midnight, with a dip in the Morning.

9. Sales and Orders by Day of the Week
```SQL
SELECT dayname(date) dayofweek, count(dayname(date)) dow_count, sum(transaction_amount) dow_revenue
FROM balaji_sales
GROUP BY dayofweek
ORDER BY dow_revenue desc;
```

![Screenshot (211)](https://github.com/YuYunusa/Balaji-Fast-Food-Analysis/assets/160647840/3722b130-fcb2-4992-8ea9-cc7e13bc6c6d)
![Screenshot (212)](https://github.com/YuYunusa/Balaji-Fast-Food-Analysis/assets/160647840/cd60ecc6-6ce7-41c8-95bc-702a3d6cc2f8)

From the above visualization, we see that sales peaked on Sundays, while Monday, Wednesday, and Thursday on the other hand are all relatively within the same range, combined are all responsible for 60% of the total revenue.. In terms of orders, Wednesday had the most orders, followed closely by Sunday and Saturday. Cumulatively they all make up 47% of the total orders placed.

10. Sales and Orders by Month
```SQL
SELECT monthname(date) monthofyear, count(dayname(date)) dow_count, sum(transaction_amount) moy_revenue
FROM balaji_sales
GROUP BY monthofyear
ORDER BY moy_revenue desc;
```

![Screenshot (215)](https://github.com/YuYunusa/Balaji-Fast-Food-Analysis/assets/160647840/2aa019b5-7067-4140-9a4e-eff22769b09d)
![Screenshot (216)](https://github.com/YuYunusa/Balaji-Fast-Food-Analysis/assets/160647840/865cd321-1377-427c-ae9f-607821b95b4f)

While not by a great margin, January was the best month as it generated the most sales, October, May, and February were all relatively within the same range, together they accounts for approximately 40% of the total revenue. May had the most orders placed, closely followed by October and then January, December, and August which had equal amount of orders placed. Combined, they all make up approximately 47% of the total orders placed.

## Recommendations
### - Promote Top Sellers:
Since Sandwich, Frankie, and Cold Coffee account for a significant portion of the revenue, consider promoting these items through special deals or combo offers to attract more customers.

### Balance Staff During Peak Times:
Given that Night and Afternoon are the busiest times, ensure that more staff are scheduled during these periods to handle the increased volume of orders efficiently.

### - Cross-Sell Beverages: 
Encourage customers to add beverages to their orders by offering discounts or combo deals. This could help increase the revenue from beverages, which currently lags behind fast food items.

### - Weekend Promotions:
Since sales peak on Sundays and orders are high on Wednesdays and Saturdays, consider running special promotions or events on these days to maximize sales.

### - January Promotions:
Since January is the best month for sales, capitalize on this by launching New Year promotions or discounts to attract more customers.

### - Off-Peak Strategies: 
For months with relatively lower sales, such as February and December, consider introducing special events, discounts, or marketing campaigns to drive traffic.

### - Gather Customer Insights: 
Collect feedback from customers about their favorite items, preferred times to visit, and overall satisfaction. Use this information to make data-driven decisions and improve service.

## Conclusion
This comprehensive analysis of Balaji's restaurant sales and order data has provided valuable insights into customer preferences, peak sales times, and effective payment methods. By leveraging these insights, Balaji can optimize its menu offerings, staff scheduling, and promotional strategies to enhance customer satisfaction and drive sales growth. Implementing the recommended strategies will not only improve operational efficiency but also strengthen Balaji's market position in the fast food industry.
Thanks for reading!
