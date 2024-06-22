# SQL-puzzles

**Q1: Given a list of employee email addresses, how can we identify the employee IDs associated with the following conditions:
     - for duplicates: If multiple email addresses exist that are identical, retain only the lowercase version and return the corresponding employee IDs. 
     - uppercase addresses: If an email address exists only once and is completely uppercase, retain it and return the corresponding employee ID.**

### Dataset:
```
CREATE TABLE students(
	id			integer,
	name		varchar(20),
	email_id		varchar(30)
)

INSERT INTO students(id, name, email_id)
VALUES (101, 'Alice Dalton', 'al.dal@abc.com'),
		(102, 'Brandon Scott', 'li.co@abc.com'),
		(103, 'Sean Mann', 'se.man@abc.com'),
		(104, 'Toby William', 'to wil@abc.com'),
		(105, 'Sam Alton', 'SA.ALT@ABC.COM'),
		(106, 'Liam Connolly', 'li.co@abc.com'),
		(107, 'Josh Day', 'LI.CO@ABC>COM')
```

### Answer:
```
WITH ranking AS (SELECT *, ASCII(email_id) ascii_value, 
				 	                    DENSE_RANK() OVER(PARTITION BY LOWER(email_id) ORDER BY ASCII(email_id) DESC) AS ranks
				          FROM students)
SELECT *
FROM students 
WHERE id NOT IN (SELECT id
				FROM ranking
				WHERE ranks > 1 AND ascii_value < 96)
```

### Query Explanation:
CTE: <br />
- ASCII() - calculates the ASCII value of the first character of the `email_id` (A-Z -> 65-90), (a-z -> 97-122).<br />
- DENSE_RANK() OVER(PARTITION BY LOWER(email_id) ORDER BY ASCII(email_id) DESC) - partitions the data by lowercase version of 'email_id` and within each partition rows are 
       ordered by the ASCII value of 'email_id` in descending order.<br />

Subquery: 
- Filters the rows where ranks are greater than 1 (this ensures that non-duplicate uppercase email_id's are not removed. Eg: id==105 where email_id is uppercase but is 
            not duplicated) and ascii_value < 96 (A-Z -> 65-90).<br />
	    
Main query: 
- Selects all rows from students table where the `id` is not in the set of `id`'s from the subquery.
    
### Result:

<img width="431" alt="Q1-ans" src="https://github.com/sowmiya-rajkumar/SQL-puzzles/assets/98767488/f0aec525-8d09-45e2-a5f9-0f5f172a5758">


<br />
<br />
******
