## Solving Case Studies Using PostgreSQL: Uber Commuter Time Analysis

<p align="justify">
  Hello! in this post I will show you how I use PostgreSQL to solve a case study example. The source of this case study can be found <a href ="https://www.interviewquery.com"> here</a>. Do keep in mind that my answer is not absolute; there could be other ways to solve it, so other answers are welcomed. Well, let's get to it!
</p>

<h2>Case Study Question</h2>
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
<p align="justify">
  First, let's understand the objective of the question. The output of this question is to get 2 variables, which are average commute time in minutes for <b>EACH</b> commuter in NY (avg_commute_time) and average commute time for <b>ALL</b> commuter in NY (avg_time). That means the value of avg_time is the same across all rows, while avg_commute_time is different according to each commuter_id. </p>
  
<p align="justify">
  After we understand the objectives, let's move on to the coding. I'm using WITH() function to save the data containing commuter time calculation for each commuter first. Since we are using PostgreSQL, I used date_part() to get the minute of both start_dt and end_dt after converting them to timestamp format first, as follows. </p>
  
```tsql
WITH data1 as(
    SELECT
        id,
        commuter_id,
        date_part('minute',end_dt::timestamp - start_dt::timestamp) as commuter_time,
        city
    FROM rides
)
```

<p align="justify">
  Next, we can move on to the main query. With the assumption that commuter_time minute should be rounded down if they're in the decimals, I used AVG() of the commuter_time calculated before, and then combine it with simple window function using OVER() for avg_time and additional partitioning for avg_commuter_time by commuter_id. Lastly, just add the filter for the city (we use 'NY' this time) and we're done. </p>

```tsql
SELECT 
    distinct(commuter_id),
    floor(avg(commuter_time) over(partition by commuter_id)) as avg_commuter_time,
    floor(avg(commuter_time) over()) as avg_time
FROM data1
WHERE city = 'NY'
```

<p align="justify"> Below is the result from the code. </p>

| commuter_id |	avg_commuter_time |	avg_time
| --- | --- | --- |
| 11	| 36	| 32 |
| 22	| 21	| 32 |
| 33	| 43	| 32 |
| 44	| 33	| 32 |
