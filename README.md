#1.Retrieve the top 3 pizzas (Pizza_Sold) based on the total revenue generated, 
   #include the pizza name, total revenue, and the sales target
   select * from sales_table;
   select * from sales_target;
   select * from branch_data;
   select a.pizza_sold Pizza_Name, sum(a.revenue) Total_Revenue, b.sales_target from sales_table a join
   sales_target b on a.pizza_sold = b.pizza group by a.pizza_sold, b.sales_target order by Total_Revenue
   desc limit 3;
   
   #2.Compare the total revenue generated for each type of pizza (Pizza_Sold) across all branches. 
   #Display the pizza name, total revenue, and the average revenue for each pizza
   select branch, pizza_sold Pizza_Name, sum(revenue) Total_Revenue, avg(revenue) Average_Revenue
   from sales_table group by branch, Pizza_Name order by branch, Pizza_Name;
   
   #3.Identify pizzas (Pizza_Sold) that are below the average sales target
   #and have generated revenue less than the overall average revenue
   select a.pizza_sold Pizza from sales_table a join sales_target b
   on a.pizza_sold = b.pizza group by a.pizza_sold having sum(a.revenue)
   < (select avg(sales_target) from sales_target) and sum(a.revenue)
   < (select avg(revenue) from sales_table); 
   
   
   
   #4.Find pizzas (Pizza_Sold) that have a price higher than the overall average price 
   #and a sales target greater than the overall average target. Exclude the 'Ikoyi' branch
   select a.pizza_sold from sales_table a join sales_target b on a.pizza_sold = b.pizza
   where price > (select avg(price) from sales_table where branch <> 'Ikoyi') 
   and sales_target > (select avg(sales_target) from sales_target where branch <> 'Ikoyi') 
   group by a.pizza_sold;
  
   
   #5.Determine the top-performing pizza (Pizza_Sold) in each branch based on total revenue. 
   #Display the branch, pizza name, and total revenue for each top performer
   with PizzaRanking as (select sales_table.branch, pizza_sold, sum(sales_table.revenue)
   as Total_Revenue, row_number() over (partition by sales_table.branch order by
   sum(sales_table.revenue) desc) as ranking from sales_table group by
   sales_table.branch, pizza_sold) select branch, pizza_sold, Total_Revenue
   from PizzaRanking where ranking = 1;
   
   #6.Retrieve the daily revenue, the daily sales target, and the variance between the revenue
   #and target for each day. Include the date and the calculated variance
   select * from sales_table;
   select * from daily_sales_target;
   select a.`date`, sum(a.revenue) Total_Daily_Revenue, b.Target, (sum(a.revenue) - b.Target) as Variance
   from sales_table a join daily_sales_target b
   on a.`date` = b.`day` group by a.`date`, b.Target;
   
   
   #7.Find the days where the total revenue exceeds the daily sales target. Include the date, total revenue,
   #and daily sales target for each exceeding day
   select a.`date`, sum(a.revenue) Total_Revenue , b.target from sales_table a join daily_sales_target b
   on a.`date` = b.`day` group by a.`date`, b.target having sum(a.revenue) > b.target;
   
   #8.Calculate the average daily revenue and identify days where the revenue is above the average. 
   #Display the date, total revenue, and daily sales target for each above-average day
   select a.`date`, sum(a.revenue) Total_Revenue, b.target from sales_table a 
   join daily_sales_target b on a.`date` = b.`day` 
   group by a.`date`, b.target having Total_Revenue > (select avg(Total_Revenue) from
   (select sum(sales_table.revenue) Total_Revenue from sales_table join daily_sales_target 
   on sales_table.`date` = daily_sales_target.`day` group by sales_table.`date`) as Avg_Revenue);
   
   #9.List the days where the total revenue falls below the daily sales target. 
   #Include the date, total revenue and daily sales target for each day below the target
   select a.`date`, sum(a.revenue) Total_Revenue, b.target from sales_table a join daily_sales_target b
   on a.`date` = b.`day` group by a.`date`, b.target having sum(a.revenue) < b.target;
   
   
  
