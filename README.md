# Pewlett-Hackard-Analysis
Columbia University intro to SQL

## Overview
This analysis uses data from a mock company's HR to create a relational database in order to determine the number of employees that will retire soon, which departments the employees work in, their titles, and if some employees could serve as mentors in a mentorship program so the company would not face such a threat from high number of retiring employees in the future and address the leadership vaccum the retirements are likely to create. 
The analysis uses SQL to create relational databases which will help keep sensitive information private while tracking information about the employees. The  assignment asked for two queries: the first showed unique titles for the retiring employees and the second showed employees eligible for a mentorship program based on their date of birth.

## Results
* From the first query, it is evident that there are 7 unique titles of the employees who are or soon will be retiring: Senior Engineer, which has the most retiring, Senior Staff which includes the second most. The other titles are Engineers, Staff, Technical Leaders, Assistant Engineer, and two managers. 

![Screen Shot 2022-05-28 at 2 30 38 PM](https://user-images.githubusercontent.com/99676466/170843106-050b66d3-1a98-4ad1-9834-80a736900a66.png)
 
* The analysis is limited because it bases the retirement information off birth dates; therefore, it makes assumptions about when people will retire, while in reality people retire at different times based on their own personal and financial goals.
 
* The mentorship eligibility analysis is likewise limited as it is based on birthdates from just one year. There are a number of other factors that go into what would make a good mentor aside from age; experience does not necessarily equal expertise.

* The mentorship program shows 1,549 people born in the year 1965 who could serve a mentors to new employees and fill roles vacated by retirees. The SQL code to find the mentors is below and was created by joining three tables, filtering for employees currently employed and born in the year 1965:

~~~ SELECT DISTINCT ON (e.emp_no) e.emp_no, 
	e.first_name,
	e.last_name,
	e.birth_date,
	de.from_date,
	de.to_date,
	t.title
--INTO mentorship_eligibility
FROM employees as e
	INNER JOIN dept_emp as de
		ON (e.emp_no = de.emp_no)
	INNER JOIN titles as t
		ON (e.emp_no = t.emp_no)
WHERE (de.to_date = '9999-01-01') 
	AND (e.birth_date BETWEEN '1965-01-01' AND '1965-12-31')
ORDER BY emp_no; 
~~~

![Screen Shot 2022-05-28 at 3 25 42 PM](https://user-images.githubusercontent.com/99676466/170843345-0fc96b4c-320c-406a-a52a-7cd1326297a5.png)

## Summary

* How many roles will need to be filled as the "silver tsunami" begins to make an impact? 
The number of retiring employees based on the data filter by birth date and hire date as shown in the code below, shows that 41,380 employees could be on the verge of retirement. 

~~~
SELECT emp_no, first_name, last_name
INTO retirement_info
FROM employees
WHERE (birth_date BETWEEN '1952-01-01' AND '1955-12-31')
AND (hire_date BETWEEN '1985-01-01' AND '1988-12-31');
-- Check the table
SELECT * FROM retirement_info;
~~~

* Are there enough qualified, retirement-ready employees in the departments to mentor the next generation of Pewlett Hackard employees?
~~~
SELECT COUNT(emp_no)
FROM mentorship_eligibility;
~~~
Using this query, the eligibility returns 1549, or significantly fewer mentors than the incoming new hires. By this criteria there are not enough eligible mentors.

In order to provide more insight, the mentorship eligibility could be expanded easily to include employees born in different years; an expanded mentorship program that filters employees based on birth year from 1964 to 1967, returned a much larger number of mentors: 19,905. 
~~~
SELECT DISTINCT ON (e.emp_no) e.emp_no, 
	e.first_name,
	e.last_name,
	e.birth_date,
	de.from_date,
	de.to_date,
	t.title
INTO expand_mentorship
FROM employees as e
	INNER JOIN dept_emp as de
		ON (e.emp_no = de.emp_no)
	INNER JOIN titles as t
		ON (e.emp_no = t.emp_no)
WHERE (de.to_date = '9999-01-01') 
	AND (e.birth_date BETWEEN '1964-01-01' AND '1967-12-31')
ORDER BY emp_no;
~~~

Because of the limitations of selecting mentors based on birthdate and hire dates, it is recommended that the department managers are tasked with selecting mentors who may be a good fit and fall outside of the filtered date data. Current managers can be returned by filtering the dept_manager database to include current managers only; below is the filtered table with a department manager listed in each department joined to the look-up table for reference on department names: 

![Screen Shot 2022-05-28 at 4 04 48 PM](https://user-images.githubusercontent.com/99676466/170844206-e257881b-da4c-48a2-b77b-fc17bb65608f.png)

The new table was created by the following query:
~~~
SELECT d.dept_no,
	d.dept_name,
	dm.emp_no,
	dm.from_date,
	dm.to_date
FROM departments AS d
INNER JOIN dept_manager AS dm
ON (d.dept_no = dm.dept_no)
WHERE to_date = '9999-01-01';
~~~
