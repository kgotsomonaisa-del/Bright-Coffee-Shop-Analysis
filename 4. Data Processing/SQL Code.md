Bright Coffee Shop Coding

---------------------------------------------------------------
--- 1. Retrieving all rows and columns from the table
---------------------------------------------------------------
select * 
from `workspace`.`casestudy1`.`bright_coffee_shop_analysis` 
limit 10;

-------------------------------------------------------------------------
--- 2. Checking the date range
-------------------------------------------------------------------------
--- 2.1 Retrieving the data collection start date - 2023-06-30
select min (transaction_date)
from `workspace`.`casestudy1`.`bright_coffee_shop_analysis`;

--- 2.2 Retrieving the data collection end date - 2023-01-01
-------------------------------------------------------------------------
select max (transaction_date)
from `workspace`.`casestudy1`.`bright_coffee_shop_analysis`;

-------------------------------------------------------------------------------
--- 3. Checking distinct store location
-------------------------------------------------------------------------------
-- we have three store location, Lower Manhattan, Hell's Kitchen and Astoria 
select distinct store_location
from `workspace`.`casestudy1`.`bright_coffee_shop_analysis`;

 
-----------------------------------------------------------------------------
--- 4. Checking products sold across the three store locations
-----------------------------------------------------------------------------
--there are nine different products sold across the three store locations
select distinct product_category
from `workspace`.`casestudy1`.`bright_coffee_shop_analysis`;

-------------------------------------------------------------------------------------------------------------------
--- 5. Checking distinct product category, product types and product details sold across the three store locations
--------------------------------------------------------------------------------------------------------------------
select distinct `product_detail`
from `workspace`.`casestudy1`.`bright_coffee_shop_analysis`;

---------------------------
select distinct product_category as category,
                product_type,
                product_detail as product_name
from `workspace`.`casestudy1`.`bright_coffee_shop_analysis`;
---------------------------

select distinct product_type
from `workspace`.`casestudy1`.`bright_coffee_shop_analysis`;

-------------------------------------------------------------------------
--- 6. Checking the minimum and maximum product prices
-------------------------------------------------------------------------
---the minimum unit price is 80c and the maximum price is R45
select (min(unit_price)) as min_price,
       (max(unit_price)) as max_price
from `workspace`.`casestudy1`.`bright_coffee_shop_analysis`;
 ----------------------------------------------------------------------------------------------------
--- 7: Which products generate the most revenue (Retrieving the Total revenue per product type)
----------------------------------------------------------------------------------------------------
---there are 29 products sold with Barista Espresso generating the most revenue, R91 406.20 and Green beans generating the least
select product_type, sum (unit_price * transaction_qty) AS total_revenue
from `workspace`.`casestudy1`.`bright_coffee_shop_analysis`
GROUP BY product_type
order by total_revenue DESC;

----------------------------------------------------------------------------------------------------
 ---8. Retrieving the number of transactions per month
 ----------------------------------------------------------------------------------------------------
 select transaction_date, count(*) as total_transactions
 from `workspace`.`casestudy1`.`bright_coffee_shop_analysis`
 group by transaction_date
 order by transaction_date;

--------------------------------------------------------------------------------------------
---9. Retrieving total sales per month
--------------------------------------------------------------------------------------------

SELECT 
    DATE_TRUNC('month', transaction_date) AS month,
    SUM(unit_price) AS total_sales
FROM `workspace`.`casestudy1`.`bright_coffee_shop_analysis`
GROUP BY month
ORDER BY month;

 
---------------------------------------------------------------------------------------------------
---10. Retrieving data on the purchase date, day name, month name, day of month, day classification, time buckets, number of sales, number of products, number of stores, revenue per day, store location, product category and product category
---------------------------------------------------------------------------------------------------
select
---Dates
      transaction_date as purchase_date,
      Dayname(transaction_date) as day_name,
      Monthname(transaction_date) as month_name,
      Dayofmonth(transaction_date) as day_of_month,
      case
          when Dayname(transaction_date) in ('Sun' , 'Sat') then 'Weekend'
          else 'Weekday'
          end as day_classification,
      ---date_format(transaction_time, 'HH:mm:ss') as purchase_time,
      case
          when date_format(transaction_time, 'HH:mm:ss') between '00:00:00' and '11:59:59' then '0.1 Morning'
          when date_format(transaction_time, 'HH:mm:ss') between '12:00:00' and '16:59:59' then '0.2 Afternoon'
          when date_format(transaction_time, 'HH:mm:ss') >= '17:00:00' then '0.3 Evening'
          end as time_buckets,
---Counts of IDs
      count(*) as number_of_sales,
      count(distinct product_id) as number_of_products,
      count(distinct store_id) as number_stores,
---Revenue
      sum(transaction_qty*unit_price) as revenue_per_day,
---Categorical Columns
      store_location,
      product_category,
      product_detail
      
from `workspace`.`casestudy1`.`bright_coffee_shop_analysis`
group by transaction_date, 
        dayname(transaction_date), 
        monthname(transaction_date), 
        dayofmonth(transaction_date),
        case
            when Dayname(transaction_date) in ('Sun' , 'Sat') then 'Weekend'
            else 'Weekday'
            end,
        case
            when date_format(transaction_time, 'HH:mm:ss') between '00:00:00' and '11:59:59' then '0.1 Morning'
            when date_format(transaction_time, 'HH:mm:ss') between '12:00:00' and '16:59:59' then '0.2 Afternoon'
            when date_format(transaction_time, 'HH:mm:ss') >= '17:00:00' then '0.3 Evening'
            end,
        store_location, 
        product_category,
        product_detail;


