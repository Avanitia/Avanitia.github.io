## Retention Analysis in PT X

<p align="justify">
Hello! in this post, I will show you a method I've done in order to visualize the retention of customers who use certain vouchers from a marketing campaign using BigQuery. Do keep in mind that this is an approach from my own perspective, so if there are other effective coding solutions, please let me know! Anyway, let's get started. </p>

<h2>Case</h2>
<p align="justify">
PT X is a startup e-grocery company which operates in Indonesia. Currently, they've finished one of their campaign with an objective to increase the retention of first-time customers through a marketing campaign. Using vouchers as a tool to promote the campaign, first_time customers will get a voucher in a specific period, tailored to their characteristics (tenure, lifetime profits, etc.) while getting the information itself by push-notification and app's inbox. A/B testing is also used to compare the effectivity of each vouchers. </p>

<p align="justify">
In order to understand the performance of their campaign, there is a need for a data visualization for customers who did use the voucher for a transaction, and then process the data to visualize how many people retain after every period of time. Below is the database scheme needed to create the analysis.</p>

<p align="center">
   <img src="https://user-images.githubusercontent.com/49559301/205889111-4ed4e082-7f3d-4237-97aa-ff57e8769bad.png" width="700" height="650" />
</p>

 <h2>Objective</h2>
 <ol type= 1>
  <li> Identify the campaign's customer pool </li>
  <li> Create a retention table </li>
  <li> Visualize customers retention</li>
  <li> Data Analysis </li>
  <li> Conclusion and Recommendation </li> 
 </ol>
 
 <h2>Tools Used</h2>
  <ol type= 1>
   <li> Bigquery </li>
   <li> Tableau </li>
   <li> Metabase </li>
  </ol>

<h2>Answer</h2>
<h3>1. Identify the campaign's customer pool </h3>
<p align="justify">
First of all, we need to get the campaign's customer pool data in order to measure the performance of the campaign, since retention rate is based of the number of people in current period (weekly/biweekly/monthly) compared to the original pool itself. Since we need information about customer pool, then data gathering would be based from consumer_voucher table (order table can't be used since the data is based on people who have done a transaction, hence not the original pool). Below is the code required to get the pool. </p>
 
 ```tsql
WITH m1_group as(
SELECT 
   DATE(created_at) AS cohort_date, 
   uid
FROM `ptx.consumer_voucher`
WHERE code = "LAKUSERU-%"
GROUP BY 1,2
),
```

<p align="justify">
Since the case is quite complex, we're using some Common Table Expression (CTE) using WITH() to contain processed informations before moving on to main query. Cohort date is used to pinpoint how many customer pool in each date for deeper retention analysis, and code filter from WHERE used % since the we need to locate the pool for the specific campaign. </p>

```tsql
get_point as(
SELECT
  cohort_date,
  COUNT(uid) as cohort_pool
FROM m1_group
GROUP BY 1
),
```

<p align="justify">
Second CTE is named get_point. Purpose of this temporary table is to store the number of customer in each date who had been given a voucher (hence a pool). The reason this is not merged using subquery in the m1_group is because m1_group is needed to be joined in another table, which is shown in the 3rd CTE. First objective is done, and we can move on to create a retention table.</p>

<h3>2. Create a retention table </h3>
<p align="justify">
Retention table is made using customer original pool as a base number, then calculate the number of customers who retain throughout the period in order to compare the retention rate from each date. Since we already have the base number from the 1st objective, the number of retained customers can be found by searching for all recorded transactions from the start of the campaign till months after, as shown below. </p>

```tsql
order_all as(
SELECT
  DISTINCT(order_no),
  user_identifier as uid,
  order_time as order_date
FROM `ptx.orders`
LEFT JOIN `ptx.orders_abuse` USING (order_no)
WHERE order_time BETWEEN '2022-09-01' AND '2022-12-31'
AND order_status NOT IN ("CREATED","CANCELLED")
AND channel IN ("ANDROID","APPLE IOS","WEB/ONLINE")
AND type IS NULL
ORDER BY 2,3
),
```

 <p align="justify">
 The 3rd CTE is named order_all. This is needed to store all customer's transaction within the start period and months after. Key takeaways from this are as follows:
   <ol type=1>
      <li> <ins>Distinct of order_no</ins>. Needed since we want unique row of each transactions, and order table can be filled with the same order_no in a different rows, since orders table is taking notes from different SKU purchased as well (1 row, 1 SKU code).</li>
      <li> <ins>Join within orders and orders_abuse</ins> to filter abused transaction (we want pure retention) using "type IS NULL" since values of type variable indicate abuse in the transaction.</li>
      <li> <ins>Filter of order_status and channel</ins>. Filter is needed to get only finished transactions and B2C channel only. </li>
   </ol></p>

```tsql
master_cohort1 as(
SELECT 
  uid,
  cohort_date,
  order_date,
  order_no,
  date_diff(order_date,cohort_date, month) as index_monthly
FROM m1_group
LEFT JOIN order_all USING (uid)
WHERE order_date >= cohort_date
ORDER BY 1,2,3
),
```

<p align="justify">
Master_cohort 1's purpose is to store information regarding customer's transaction <b>AFTER OR SAME DAY</b> as the cohort date (since we want to see retention after cohort). To do that, we need to join m1_group and order_all first. Then, the gap between each transaction (index_monthly) is calculated by subtracting cohort_date and order_date by month. Now we can track customer's retention monthly, but we need to make it easier to read, which is why we'll be moving to the main query now.</p>

```tsql   
master_cohort2 as(
SELECT  
  cohort_date,
  index_monthly,
  count(uid) as total_uid
FROM master_cohort1
group by 1,2
)
```

<p align="justify">
The last CTE is master_cohort2, with purpose to hold the information of total customers retained (same idea as get_point, but different target). Since we have all the informations we needed, let's move on to main query.</p>

```tsql
SELECT
  cohort_date,
  cohort_pool,
  MAX(IF(index_monthly = 0, total_uid,NULL)) as index_0,
  MAX(IF(index_monthly = 1, total_uid,NULL)) as index_1,
  MAX(IF(index_monthly = 2, total_uid,NULL)) as index_2,
  MAX(IF(index_monthly = 3, total_uid,NULL)) as index_3,
  MAX(IF(index_monthly = 4, total_uid,NULL)) as index_4
FROM get_point
LEFT JOIN master_cohort2 USING (cohort_date)
GROUP BY 1,2
ORDER BY 1
```
<p align="justify">
In main query, we compile all the information from get_point and master_cohort2 by joining them, then used MAX() function combined with IF() to get total customers from each cohort_date. With that, the second objective is done. You can see the results below. </p>

<p align="center">
<img src="https://user-images.githubusercontent.com/49559301/205937308-74339ea4-1b6f-46b7-9e11-0a139b462517.png" width="300" height="275" />
</p>

<h3>3. Visualize customers retention </h3>




