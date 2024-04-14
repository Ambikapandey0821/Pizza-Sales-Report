# PIZZA SALES REPORT

# PROBLEM STATEMENT
THIS REPORT LEVERAGES SQL QUERIES WITHIN SSMS TO COMPREHENSIVELY ANALYZE PIZZA SALES DATA. THE ANALYSIS AIMS TO IDENTIFY KEY TRENDS, CUSTOMER PREFERENCES, AND
AREAS FOR IMPROVEMENT, ULTIMATELY EMPOWERING DATADRIVEN DECISION-MAKING TO BOOST SALES PERFORMANCE,OPTIMIZE OPERATIONS, SUPPORT STRATEGIC PLANNING.

1. RETRIEVE THE TOTAL NUMBER OF ORDERS PLACED.
   
         select COUNT(order_id) as Total_Orders from [dbo].[orders]
   
   ![image](https://github.com/Ambikapandey0821/Pizza-Sales-Report/assets/162020155/151be898-6b17-4fba-bbfe-751fe7d409ea)


3. Calculate the total revenue generated from pizza sales.

         select round(sum(od.quantity*p.price),2) as Total_revenue from
         [dbo].[pizzas] p inner join [dbo].[order_details] od on od.pizza_id=p.pizza_id
   
   ![image](https://github.com/Ambikapandey0821/Pizza-Sales-Report/assets/162020155/20300c5d-4636-422d-aa27-4f7e0197fabb)


5. Identify the highest-priced pizza.

         select * from pizzas where price = (select MAX(price) from pizzas)

   ![image](https://github.com/Ambikapandey0821/Pizza-Sales-Report/assets/162020155/1e4ac2f8-a4bc-4ff2-983e-69279298ed9f)


7. Identify the most common pizza size ordered.

         select top 1 p.size, sum(od.quantity) as Order_count from pizzas p 
         inner join [dbo].[order_details] od on p.pizza_id=od.pizza_id
         group by p.size order by sum(od.quantity) desc;

   ![image](https://github.com/Ambikapandey0821/Pizza-Sales-Report/assets/162020155/e7661c9d-08d9-4973-8f38-334c3685d9ba)

8. List the top 5 most ordered pizza types along with their quantities.

         select top 5 pt.Name, SUM(od.quantity) as Total_orders
         from
         [dbo].[order_details] od inner join [dbo].[pizzas] p on p.pizza_id=od.pizza_id inner join [dbo].[pizza_types] pt
         on pt.pizza_type_id=p.pizza_type_id
         group by pt.name
         order by SUM(od.quantity) desc

   ![image](https://github.com/Ambikapandey0821/Pizza-Sales-Report/assets/162020155/7c0330f4-98e9-4cd9-8acd-197cea2c875a)


10. Join the necessary tables to find the total quantity of each pizza category ordered.
   
         select pt.Category, SUM(od.quantity) as Total_Quantity
         from
         [dbo].[order_details] od inner join [dbo].[pizzas] p on p.pizza_id=od.pizza_id inner join [dbo].[pizza_types] pt
         on pt.pizza_type_id=p.pizza_type_id
         group by pt.category
         order by SUM(od.quantity) desc

    ![image](https://github.com/Ambikapandey0821/Pizza-Sales-Report/assets/162020155/1c7a8e13-f3f3-4eb1-90ac-062b1bca50ce)


12. Determine the distribution of orders by hour of the day.
   
         select datepart(hour,time) as Order_Time, count(order_id) as Orders_Per_Hour from [orders] 
         group by datepart(hour,time) order by Orders_Per_Hour desc

    ![image](https://github.com/Ambikapandey0821/Pizza-Sales-Report/assets/162020155/99ae104f-0a83-4f60-8004-77dc0d001cbc)


14. Determine the distribution of orders by day of the week.

         select Order_Day, AVG(Orders_Per_Day) as Avg_Orders_Per_Day from
         (select datename(dw,date) as Order_Day, count(order_id) as Orders_Per_Day from [orders] 
         group by datename(dw,date)) A 
         group by Order_Day

    ![image](https://github.com/Ambikapandey0821/Pizza-Sales-Report/assets/162020155/cc67ce1d-a397-4a44-9ad3-dfeda157893b)


16. Group the orders by date and calculate the average number of pizzas ordered per day.

         select avg(Total_Orders_per_day) as Avg_Orders_per_day from
         	(select o.date as Order_date, sum(od.quantity) as Total_Orders_per_day
         	from [dbo].[order_details] od
         	inner join [dbo].[orders] o on od.order_id=o.order_id
         	group by o.date ) total_orders

    ![image](https://github.com/Ambikapandey0821/Pizza-Sales-Report/assets/162020155/63731bc8-7a27-4c7c-b23c-da04f3fd8ae5)


18. Determine the top 3 most ordered pizza types based on revenue.

         select top 3 pt.name, sum(p.price*Od.quantity) as revenue
         from [dbo].[pizzas] p
         inner join [dbo].[order_details] od
         on p.pizza_id=OD.pizza_id inner join [dbo].[pizza_types] pt on pt.pizza_type_id =p.pizza_type_id
         group by pt.name
         order by sum(p.price*Od.quantity) desc

    ![image](https://github.com/Ambikapandey0821/Pizza-Sales-Report/assets/162020155/4b38cea5-a5d3-4496-911e-cf6c7536bb48)


20. Calculate the percentage contribution of each pizza type to total revenue.

         select pt.category, 
         concat(round(sum(p.price*od.quantity)*100/ ( select sum(p.price*od.quantity) as Total_Revenue
         	from 
         	[dbo].[order_details] od inner join [dbo].[pizzas] p on od.pizza_id=p.pizza_id),2),'%') as Revenue_per_category
         from [dbo].[pizza_types] pt inner join [dbo].[pizzas] p on p.pizza_type_id=pt.pizza_type_id 
         inner join [dbo].[order_details] od on od.pizza_id=p.pizza_id
         group by pt.category order by Revenue_per_category desc

    ![image](https://github.com/Ambikapandey0821/Pizza-Sales-Report/assets/162020155/5cbefda0-1858-4c68-a7d4-ab34fc250806)


22. Analyze the cumulative revenue generated over time.

         select Order_date,round(Revenue,2) as Revenue_Per_Day, round(SUM(revenue) over(order by order_date),2) as Cumulative_Revenue from 
         (select o.date as order_date, sum(p.price*Od.quantity) as revenue
         from [dbo].[pizzas] p
         inner join [dbo].[order_details] od
         on p.pizza_id=OD.pizza_id inner join[dbo].[orders] o on o.order_id =od.order_id
         group by o.date) new_sales

    ![image](https://github.com/Ambikapandey0821/Pizza-Sales-Report/assets/162020155/b5b0fd18-1abf-4696-8e80-45ce4bba33d1)


24. Determine the top 3 most ordered pizza types based on revenue for each pizza category.

         with cte as(
         select pt.Category,pt.Name , sum(p.price*Od.quantity) as Revenue,  dense_rank() over(partition by pt.category order by sum(p.price*Od.quantity) desc) as RN
         from [dbo].[pizzas] p
         inner join [dbo].[order_details] od
         on p.pizza_id=OD.pizza_id inner join [dbo].[pizza_types] pt on pt.pizza_type_id =p.pizza_type_id
         group by pt.name, pt.category)
         select * from cte where RN<=3

![image](https://github.com/Ambikapandey0821/Pizza-Sales-Report/assets/162020155/c6510ac9-a4e1-47ab-9afb-f7adc67fe2b9)

# INSIGHTS
- TOTAL ORDERS - 21350
- TOTAL REVENUE - 817860
- HIGHEST PRICED PIZZA - 35.95
- MOST COMMON SIZE PIZZA ORDERED AND ORDER NUMBER - L AND 18956
- TOP SELLING PIZZA NAME - THE CLASSIC DELUXE PIZZA
- TOP SELLING PIZZA CATEGORY AND QUANTITY - CLASSIC, 148888
- TOP ORDERED PIZZA TIME - 12, 2520
- DAY OF THE WEEK WITH HIGHEST AVERAGE PIZZA SOLD - SATURDAY, 3158
- AVERAGE ORDERS PER DAY - 138
- TOP 3 PIZZA TYPES BY REVENUE- THE THAI CHICKEN PIZZA, THE BARBECUE CHICKEN PIZZA, THE CALIFORNIA CHICKEN PIZZA
- TYPE AND % OF TOP SELLING PIZZA - CLASSIC, 26.91%
