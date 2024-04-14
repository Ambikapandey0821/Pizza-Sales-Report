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


