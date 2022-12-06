## Solving Case Studies Using PostgreSQL

<p align="justify">
  Hello! in this post I will show you how I use PostgreSQL to solve some case studies example. The source of these case studies can be found on https://www.interviewquery.com. Do keep in mind that my answer is not absolute; there could be other ways to solve it, so other answers are welcomed. Well, let's get to it!
</p>

<h2>Case 1: Uber Commuter Time Analysis</h2>
<p align="justify">
  Let’s say you work at Uber. The rides table contains information about the trips of Uber users across America. The table is as follows.</p>
  
  | Column | Type |
  | ------ | ---- |
  | id | INTEGER |
  | commuter_id | INTEGER |
  | start_dt | DATETIME |
  | end_dt | DATETIME |
  | city | VARCHAR |

<p align="justify">
  Write a query to get the average commute time (in minutes) for each commuter in New York and the average commute time (in minutes) across all commuters in   New York.</p> 
  
<h3>Answer</h3>

  
```psql
WITH data1 as(
    SELECT
        id,
        commuter_id,
        date_part('minute',end_dt::timestamp - start_dt::timestamp) as commuter_time,
        city
    FROM rides
)

SELECT 
    distinct(commuter_id),
    floor(avg(commuter_time) over(partition by commuter_id)) as avg_commuter_time,
    floor(avg(commuter_time) over()) as avg_time
FROM data1
WHERE city = 'NY'
```
