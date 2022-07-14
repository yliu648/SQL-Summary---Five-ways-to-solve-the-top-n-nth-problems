# SQL-Summary---Five-ways-to-solve-the-top-n-nth-problems

Leetcode Q176. Second Highest Salary


![image](https://user-images.githubusercontent.com/60156824/178890278-44d6a151-ae97-45ea-8397-c35165e3a103.png)


This problem is similar to problems:
177, 185, 184, 1194, 1341, 586, 1549, 1532, 1369, 1097, 1077, 1070, 602, 1082, 1112, 619, 574 (most voted)

Also, this question is similar to 619 on how to deal with null value. IFNULL(val, null) will not return 'null' if the value is empty (IFNULL will assume empty is the value we want). To avoid this problem, use either MAX() or subquery (IFNULL((subquery), null).

There are five ways to solve this type of problems:

1. Calculate aggregated values first to find the max/min/most/earliest (in either subquery or CTE), and then select records with values equal to (or smaller than in this problem) the aggregated values.
```
SELECT DISTINCT MAX(salary) AS SecondHighestSalary
FROM Employee a
WHERE Salary< (SELECT MAX(salary) FROM Employee b WHERE b.salary > a.salary)
```
 
2. Using window function (RANK, DENSE_RANK, ROW_NUMBER, MAX(), MIN()) to append ranks, and the select records with values equal to rank n. (use dense_rank if more than one employee has the maximum salary
```
WITH CTE AS
			(SELECT Salary, RANK () OVER (ORDER BY Salary desc) AS RANK_desc
			   FROM Employee)
SELECT MAX(salary) AS SecondHighestSalary
FROM CTE
WHERE RANK_desc = 2
 ```

3. ORDER BY value, LIMIT n-1 OFFSET n or use TOP n (can also find the 2nd by using TOP 2 + SELECT MIN())
```
SELECT IFNULL((SELECT DISTINCT Salary
	             FROM Employee 
				 ORDER BY Salary DESC 
				 LIMIT 1,1),NULL) AS SecondHighestSalary
```

4. (Apply to find the 2nd): SELECT MAX(b.val) FROM table a, table b WHERE a.val > b.val
```
SELECT
MAX(a.Salary) as SecondHighestSalary
FROM Employee a
JOIN Employee b
ON a.Salary < b.Salary
```

5. Nested query: SELECT val FROM table a WHERE n > (SELECT val FROM table b WHERE b.val > a.val). If using this to find the 2nd, need to combine the third way above (ORDER BY, LIMIT, OFFSET) to remove the 1st.
```
SELECT DISTINCT salary
FROM Employee a
WHERE 2> (SELECT COUNT(DISTINCT salary) FROM Employee b WHERE b.salary > a.salary )
ORDER BY 1 DESC
LIMIT 2 OFFSET 1
 ```
 
 
