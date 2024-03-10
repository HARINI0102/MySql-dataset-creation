# MySql-dataset-creation
zomato dataset and namma yatri dataset
*********ZOMATO DATASET QUESTION SOLVED******

"1 .what is the total price of each customer spend on zomato?"

select sales.userid,
sum(product.price) total_amount_spend from sales
inner join product
on sales.product_id = product.product_id
group by sales.userid

"2. how many days customer visited zomato?"

select userid,count(distinct created_date) no_of_vist from sales group by userid

"3. what was the first product purchased by each customer?"

select * from
(select *,rank() over(partition by userid order by created_date) rnk from sales) sales 

where rnk = 1

4."what is the most purchased item in the menu and how mant times it is been purchased?"


select product_id,count(product_id) from sales group by product_id order by product_id asc

 limit 1

5. "which item is most popular for each customer?"

select * from
select *,rank() over(partition on userid order by created_date) rnk from 
(select a.userid,a.product_id,count(product_id) from sales a  group by userid,product_id)

6." which item was purchased first by customer after they became member?"

select * from
(select *,rank() over(partition by userid order by created_date) rnk from 
(select a.userid,a.created_date,a.product_id,b.gold_signup_date from sales a 
inner join goldusers_signup b
on a.userid=b.userid and created_date>= gold_signup_date)c) d 
where rnk =1

7. "which item was purchased first by customer before they became member?"

select * from
(select *,rank() over(partition by userid order by created_date) rnk from 
(select a.userid,a.created_date,a.product_id,b.gold_signup_date from sales a 
inner join goldusers_signup b
on a.userid=b.userid and created_date<= gold_signup_date)c) d 
where 
rnk =1
doctors


########### NAMMA YATRI QUESTION SOLVED ##########

--1.  which duration had more trips
	select* from 
    (select *,rank() over(order by cnt desc ) rnk from 
    (select duration,count(distinct tripid) cnt from trips
    group by duration)b)c
    where rnk=1;
    
--2. which driver , customer pair had more orders
select* from 
    (select *,rank() over(order by cnt desc ) rnk from 
    (select driverid,custid,count(distinct tripid) cnt from trips
    group by driverid,custid)c)d
    where rnk=1;

--3. search to estimate rate
select *from trips_details4;
select (sum(searches_got_estimate)*100.0/sum(searches)) est_rate from trips_details4;


-- 4. which area got highest trips in which duration
select * from
(select *,rank() over (partition by duration order by cnt desc ) rnk from 
(select duration,loc_from,count(distinct tripid) cnt from trips
group by duration,loc_from)a)c 
where rnk =1
;

select * from
(select *,rank() over (partition by loc_from order by cnt desc ) rnk from 
(select duration,loc_from,count(distinct tripid) cnt from trips
group by duration,loc_from)a)c 
where rnk =1
;

--5.  which area got the highest fares, cancellations,trips,
select *from trips;
select * from trips_details4;
select * from (select * ,rank () over (order by fare desc ) rnk 
from 
( select loc_from, sum (fare) fare from trips 
group by loc_from)b)c
where  rnk = 1;

select * from (select * ,rank () over (order by can desc ) rnk 
from 
( 
select loc_from, count(*) - sum(driver_not_cancelled) can
from trips_details4
group by loc_from )b)c
where rnk = 1;


-- 6. which duration got the highest trips and fares
select * from
(select *,rank() over ( order by fare desc ) rnk from 
(select duration,count(distinct tripid) fare from trips
group by duration)b)c 
where rnk =1
;
