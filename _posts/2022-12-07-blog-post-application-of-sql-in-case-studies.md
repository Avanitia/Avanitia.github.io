#Application of SQL in Case Studies
Let’s say you work at Uber. The rides table contains information about the trips of Uber users across America

Write a query to get the average commute time (in minutes) for each commuter in New York and the average commute time (in minutes) across all commuters in New York.

Example:

Input:

rides table
![image](https://user-images.githubusercontent.com/49559301/205698192-24a12c59-74a3-407f-b8a9-89033216909d.png)

Output:
![image](https://user-images.githubusercontent.com/49559301/205698232-08a5dc92-7e56-48e5-b8c3-1145d7165ec6.png)

Display results like:
![image](https://user-images.githubusercontent.com/49559301/205698301-2086fd83-335d-42ea-8707-bc2f87be8a01.png)

Source: https://www.interviewquery.com/questions/average-commute-time

Answer:
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
