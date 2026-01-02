

<img width="334" height="187" alt="image" src="https://github.com/user-attachments/assets/ff4c5c2b-45bc-4ccc-a93b-2c82d14df190" />

1) Provide the name of the sales_rep in each region with the largest amount of total_amt_usd sales

2) For the region with the largest sales total_amt_usd, how many total orders were placed?

3) How many accounts had more total purchases than the account name which has bought the most standard_qty paper throughout their lifetime as a customer?

4) For the customer that spent the most (in total over their lifetime as a customer) total_amt_usd, how many web_events did they have for each channel?

5) What is the lifetime average amount spent in terms of total_amt_usd for the top 10 total spending accounts?

6) What is the lifetime average amount spent in terms of total_amt_usd, including only the companies that spent more per order, on average, than the average of all orders.

   

1) Provide the name of the sales_rep in each region with the largest amount of total_amt_usd sales


WITH t1 AS (
     SELECT s.name rep_name, r.name region_name, SUM(o.total_amt_usd) total_amt
      FROM sales_reps s
      JOIN accounts a
      ON a.sales_rep_id = s.id
      JOIN orders o
      ON o.account_id = a.id
      JOIN region r
      ON r.id = s.region_id
      GROUP BY 1,2
      ORDER BY 3 DESC), 
t2 AS (
      SELECT region_name, MAX(total_amt) total_amt
      FROM t1
      GROUP BY 1)
SELECT t1.rep_name, t1.region_name, t1.total_amt
FROM t1
JOIN t2
ON t1.region_name = t2.region_name AND t1.total_amt = t2.total_amt

<img width="526" height="387" alt="Screen Shot 2026-01-02 at 18 14 57" src="https://github.com/user-attachments/assets/20abc08c-6446-448a-a076-da75caf84e90" />


2) For the region with the largest sales total_amt_usd, how many total orders were placed?
   
 select distinct  count (o.id) as number_orders
from orders o 
left join accounts a 
on o.account_id=a.id
left join sales_reps  s
on a.sales_rep_id=s.id
left join region r
on region_id=r.id
 where r.name='West'
 
<img width="468" height="161" alt="image" src="https://github.com/user-attachments/assets/47dd7e8c-17fd-4762-8698-9512f7254ddd" />

3) How many accounts had more total purchases than the account name which has bought the most standard_qty paper throughout their lifetime as a customer?
WITH  t1 as (select a.name,sum(o.standard_qty) as total_quantity
from orders o
left join accounts a 
on o.account_id=a.id
group by a.name
order by  sum(o.standard_qty) desc
limit 1),

t2 as (select a.name as name, sum(total) as total_purchase
from orders o 
left join accounts a 
on o.account_id=a.id
group  by a.name)

select count(*)
from t2
where total_purchase>(select total_quantity from t1)

<img width="468" height="291" alt="image" src="https://github.com/user-attachments/assets/88054d60-625d-46b0-961a-dc3d2eddf21c" />

 

4. For the customer that spent the most (in total over their lifetime as a customer) total_amt_usd, how many web_events did they have for each channel?


WITH t1 as (select sum (total_amt_usd) total_spent, a.id as customer_id
from orders o 
left join accounts a 
on o.account_id=a.id
left join web_events w
on a.id=w.account_id
group by a.id
order by total_spent desc
limit 1)
select count(w.account_id), channel
	  from web_events w
	  where w.account_id=(select customer_id from t1)
	  group by channel

<img width="468" height="254" alt="image" src="https://github.com/user-attachments/assets/037548b7-47ee-4ade-9368-f8dbb5897a64" />


 

5--What is the lifetime average amount spent in terms of total_amt_usd for the top 10 total spending accounts?

WITH t1 AS (select  account_id, sum(total_amt_usd) total_spent
from orders
group by account_id
order by sum(total_amt_usd) desc 
limit 10)

SELECT AVG(total_spent) average_spent
from t1

<img width="468" height="174" alt="image" src="https://github.com/user-attachments/assets/7e200c05-0000-4255-b6f6-66fa93103013" />
 
6)	What is the lifetime average amount spent in terms of total_amt_usd, including only the companies that spent more per order, on average, than the average of all orders.

WITH t1 AS ( SELECT AVG(o.total_amt_usd) 
			avg_all FROM orders o 
			JOIN accounts a ON a.id = o.account_id),
t2 AS ( SELECT o.account_id, AVG(o.total_amt_usd) avg_amt
	   FROM orders o
	    GROUP BY 1 
	   HAVING AVG(o.total_amt_usd) > (SELECT * FROM t1))
SELECT AVG(avg_amt) FROM t2

<img width="468" height="636" alt="image" src="https://github.com/user-attachments/assets/87026f27-9f92-4a38-affe-5bc13ad75415" />


