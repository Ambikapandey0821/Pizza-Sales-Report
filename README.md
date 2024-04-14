# PIZZA SALES REPORT

# PROBLEM STATEMENT
THIS REPORT LEVERAGES SQL QUERIES WITHIN SSMS TO COMPREHENSIVELY ANALYZE PIZZA SALES DATA. THE ANALYSIS AIMS TO IDENTIFY KEY TRENDS, CUSTOMER PREFERENCES, AND
AREAS FOR IMPROVEMENT, ULTIMATELY EMPOWERING DATADRIVEN DECISION-MAKING TO BOOST SALES PERFORMANCE,OPTIMIZE OPERATIONS, SUPPORT STRATEGIC PLANNING.

1. RETRIEVE THE TOTAL NUMBER OF ORDERS PLACED.
   
         select COUNT(order_id) as Total_Orders from [dbo].[orders]

2. Calculate the total revenue generated from pizza sales.

         select round(sum(od.quantity*p.price),2) as Total_revenue from
         [dbo].[pizzas] p inner join [dbo].[order_details] od on od.pizza_id=p.pizza_id

3. Identify the highest-priced pizza.

         select * from pizzas where price = (select MAX(price) from pizzas)

4. Identify the most common pizza size ordered.

         select top 1 p.size, sum(od.quantity) as Order_count from pizzas p 
         inner join [dbo].[order_details] od on p.pizza_id=od.pizza_id
         group by p.size order by sum(od.quantity) desc;
   
5. List the top 5 most ordered pizza types along with their quantities.

         select top 5 pt.Name, SUM(od.quantity) as Total_orders
         from
         [dbo].[order_details] od inner join [dbo].[pizzas] p on p.pizza_id=od.pizza_id inner join [dbo].[pizza_types] pt
         on pt.pizza_type_id=p.pizza_type_id
         group by pt.name
         order by SUM(od.quantity) desc

6. Join the necessary tables to find the total quantity of each pizza category ordered.
   
         select pt.Category, SUM(od.quantity) as Total_Quantity
         from
         [dbo].[order_details] od inner join [dbo].[pizzas] p on p.pizza_id=od.pizza_id inner join [dbo].[pizza_types] pt
         on pt.pizza_type_id=p.pizza_type_id
         group by pt.category
         order by SUM(od.quantity) desc

7. Determine the distribution of orders by hour of the day.
   
         select datepart(hour,time) as Order_Time, count(order_id) as Orders_Per_Hour from [orders] 
         group by datepart(hour,time) order by Orders_Per_Hour desc

8. Determine the distribution of orders by day of the week.

         select Order_Day, AVG(Orders_Per_Day) as Avg_Orders_Per_Day from
         (select datename(dw,date) as Order_Day, count(order_id) as Orders_Per_Day from [orders] 
         group by datename(dw,date)) A 
         group by Order_Day

9. Group the orders by date and calculate the average number of pizzas ordered per day.

         select avg(Total_Orders_per_day) as Avg_Orders_per_day from
         	(select o.date as Order_date, sum(od.quantity) as Total_Orders_per_day
         	from [dbo].[order_details] od
         	inner join [dbo].[orders] o on od.order_id=o.order_id
         	group by o.date ) total_orders

10. Determine the top 3 most ordered pizza types based on revenue.

         select top 3 pt.name, sum(p.price*Od.quantity) as revenue
         from [dbo].[pizzas] p
         inner join [dbo].[order_details] od
         on p.pizza_id=OD.pizza_id inner join [dbo].[pizza_types] pt on pt.pizza_type_id =p.pizza_type_id
         group by pt.name
         order by sum(p.price*Od.quantity) desc

11. Calculate the percentage contribution of each pizza type to total revenue.

         select pt.category, 
         concat(round(sum(p.price*od.quantity)*100/ ( select sum(p.price*od.quantity) as Total_Revenue
         	from 
         	[dbo].[order_details] od inner join [dbo].[pizzas] p on od.pizza_id=p.pizza_id),2),'%') as Revenue_per_category
         from [dbo].[pizza_types] pt inner join [dbo].[pizzas] p on p.pizza_type_id=pt.pizza_type_id 
         inner join [dbo].[order_details] od on od.pizza_id=p.pizza_id
         group by pt.category order by Revenue_per_category desc

12. Analyze the cumulative revenue generated over time.

         select Order_date,round(Revenue,2) as Revenue_Per_Day, round(SUM(revenue) over(order by order_date),2) as Cumulative_Revenue from 
         (select o.date as order_date, sum(p.price*Od.quantity) as revenue
         from [dbo].[pizzas] p
         inner join [dbo].[order_details] od
         on p.pizza_id=OD.pizza_id inner join[dbo].[orders] o on o.order_id =od.order_id
         group by o.date) new_sales

13. Determine the top 3 most ordered pizza types based on revenue for each pizza category.

         with cte as(
         select pt.Category,pt.Name , sum(p.price*Od.quantity) as Revenue,  dense_rank() over(partition by pt.category order by sum(p.price*Od.quantity) desc) as RN
         from [dbo].[pizzas] p
         inner join [dbo].[order_details] od
         on p.pizza_id=OD.pizza_id inner join [dbo].[pizza_types] pt on pt.pizza_type_id =p.pizza_type_id
         group by pt.name, pt.category)
         select * from cte where RN<=3
