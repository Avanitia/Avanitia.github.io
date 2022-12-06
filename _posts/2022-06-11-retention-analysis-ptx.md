## Retention Analysis in PT X

<p align="justify">
Hello! in this post, I will show you a method I've done in order to visualize the retention of customers who use certain vouchers from a marketing campaign using BigQuery. Do keep in mind that this is an approach from my own perspective, so if there are other effective coding solutions, please let me know! Anyway, let's get started. </p>

<p align="justify">
PT X is a startup e-grocery company which operates in Indonesia. Currently, they've finished one of their campaign with an objective to increase the retention of first-time customers through a marketing campaign. Using vouchers as a tool to promote the campaign, first_time customers will get a voucher in a specific period, tailored to their characteristics (tenure, lifetime profits, etc.) while getting the information itself by push-notification and app's inbox. A/B testing is also used to compare the effectivity of each vouchers. </p>

<p align="justify">
In order to understand the performance of their campaign, there is a need for a data visualization for customers who did use the voucher for a transaction, and then process the data to visualize how many people retain after every period of time. Below is the database scheme needed to create the analysis.</p>

<p align="center">
   <img src="https://user-images.githubusercontent.com/49559301/205889111-4ed4e082-7f3d-4237-97aa-ff57e8769bad.png" width="700" height="650" />
</p>

 <h2>Task</h2>
 <ol type= 1>
  <li> Identify first-time customers in the campaign who redeem their voucher for a transaction </li>
  <li> Create a retention table </li>
  <li> Visualize customers retention into a graph for easier analysis </li>
 </ol>

 <h2>Answer</h2>
  <h3>1. Identify first-time customers in the campaign who redeem their voucher for a transaction </h3>
  
  
  


```tsql
WITH m1_group as(
SELECT `date` AS cohort_date, uid, date_sub(`date`, INTERVAL 7 DAY) as first_transaction_date
FROM `harvest.growth_p2_experimental`
WHERE experiment = "M1"
AND `group` = "Control"
GROUP BY 1,2
),

order_all as(
SELECT
  distinct(order_no),
  a.uid,
  date(order_date) as order_date,
  ROW_NUMBER() OVER (PARTITION BY a.uid ORDER BY order_date) as order_count,
  COUNT(a.uid) OVER (PARTITION BY a.uid) as total_order,
  SUM(voucher_counter) OVER (PARTITION BY a.uid) as voucher_sum,
  code,
  a.discount_amount,
  minimum_transaction,
  total_price,
  CASE
    WHEN a.discount_amount > minimum_transaction AND minimum_transaction = 0 THEN NULL
    WHEN a.discount_amount = 0 AND minimum_transaction = 0 THEN NULL
    ELSE a.discount_amount/minimum_transaction END AS perc_discount
FROM (SELECT
            DISTINCT(order_no),
            user_identifier as uid,
            order_time as order_date,
            total_price,
            voucher_code AS code,
            discount_amount,
            CASE
              WHEN voucher_code IS NOT NULL THEN 1
              ELSE 0 END AS voucher_counter
      FROM `sayurbox_bi_prod.orders`
      WHERE order_time BETWEEN '2022-09-01' AND '2022-12-31'
      AND order_status NOT IN ("CREATED","CANCELLED")
      AND channel IN ("SAYURBOX APP - ANDROID","SAYURBOX APP - APPLE IOS","WEB/ONLINE")
      ) as a
LEFT JOIN `granary.consumer_vouchers`  as b
USING (code)
ORDER BY 2,3
),

get_point as(
SELECT
  cohort_date,
  COUNT(uid) as cohort_pool
FROM m1_group
GROUP BY 1
),

master_cohort1 as(
SELECT 
  uid,
  cohort_date,
  order_date,
  order_no,
  total_price,
  discount_amount,
  minimum_transaction,
  perc_discount,
  date_diff(order_date,cohort_date, week) as index_weekly
FROM m1_group
LEFT JOIN order_all USING (uid)
WHERE order_date >= cohort_date
AND voucher_sum > 0
ORDER BY 1,2,3
),

master_cohort2 as(
SELECT  
  cohort_date,
  index_weekly,
  count(uid) as total_uid,
  MIN(minimum_transaction) as min_requirement_aov,
  MAX(minimum_transaction) as max_requirement_aov,
  AVG(minimum_transaction) as avg_requirement_aov,
  AVG(discount_amount) as avg_discount,
  AVG(perc_discount) as avg_perc,
  AVG(total_price) as aov
FROM master_cohort1
group by 1,2
)

SELECT
  cohort_date,
  cohort_pool,
  MAX(IF(index_weekly = 0, total_uid,NULL)) as index_0,
  MAX(IF(index_weekly = 1, total_uid,NULL)) as index_1,
  MAX(IF(index_weekly = 2, total_uid,NULL)) as index_2,
  MAX(IF(index_weekly = 3, total_uid,NULL)) as index_3,
  MAX(IF(index_weekly = 4, total_uid,NULL)) as index_4,
  MAX(IF(index_weekly = 5, total_uid,NULL)) as index_5,
  MAX(IF(index_weekly = 6, total_uid,NULL)) as index_6,
  MAX(IF(index_weekly = 7, total_uid,NULL)) as index_7,
  MAX(IF(index_weekly = 8, total_uid,NULL)) as index_8
FROM get_point
LEFT JOIN master_cohort2 USING (cohort_date)
GROUP BY 1,2
ORDER BY 1
```
