# Pewlett Hackard Analysis

## Overview of Analysis

Pewlett Hackard is facing a 'silver tsunami' with many of it's employees nearing retirment age.  We have been asked to help analyze the situation so that Pewlett Hackard can better understand where the impacts will be and how to best manage those impacts.  The analysis was completed using PostgreSQL and data provided by Pewlett Hackard.  

## Results

From the Employee and Titles files a Retirement Titles table was created to list the names, birth date, and title of current employees specifically with birth dates between 1952 and 1955 (i.e. those nearing retirment age).

This data then needed to be cleansed because duplicate lines appeared if an employee had more than one title in the time they have worked for Pewlett Hackard.  The DISTINCT ON function was used to filter to only the current title for each employee to create a Unique Titles table.  This table was then used with the GROUP BY function to create a more informative summary table by titles showing the number of employees by Title that are nearing retirmeent.  

| count | title              |
|-------|--------------------|
| 29414 | Senior Engineer    |
| 28255 | Senior Staff       |
| 14222 | Engineer           |
| 12242 | Staff              |
| 4500  | Technique Leader   |
| 1761  | Assistant Engineer |
| 4     | Manager            |

With ~90,000 employees approaching retirement age Pewlett Hackard knows they need to make a plan for this large transition so they asked for information on employees eligible for a mentorship program.  They asked to see the employees born in 1965 that could be mentored to replace the employees that will retire. This prdouced a list of ~1,500 employees in the table Mentorship Eligibility.

From the analysis completed there are 4 major points seen:

1. ~90,000 employees are close to retirment age.

2. Most of the employees nearing retirment age are Senior Staff or Senior Engineers (i.e. highly experienced not in entry type roles).

3. By the criteria given there are ~1,500 mentorship eligible employees; far fewer than needed to fill the void of retiring employees.

4. The cleansed titles table has ~40,000 few lines than the first titles table but the cleansed titles table has ~90,000 names which suggests many of the senior employees were hired into those roles from outside the organization and did not advance through various roles within Hewlett Packard to get to that role. 

## Summary

Hewlett Packard has ~90,000 roles that wil need to be filed as employees retire in the next couple of years.  Are there enough qualified retirment ready employees to mentor the next generation of Pewlett Hackard employees?  The answer to that question is yes.  But, based on the criteria given for the mentorship program eligibility it appears there are not enough employees to mentor.   I've added a table summarzing the Mentorship Elibility table by title so the challenge Pewlett Hackard faces is easier to visualize.  

SELECT COUNT (emp_no), title
INTO mentorship_eligibility_summary
FROM mentorship_eligibility
GROUP BY title
ORDER BY COUNT (emp_no) DESC;



| count | title              |
|-------|--------------------|
| 569   | Senior Staff       |
| 501   | Engineer           |
| 169   | Senior Engineer    |
| 155   | Staff              |
| 78    | Assistant Engineer |
| 77    | Technique Leader   |

Given that the provided criteria produced a far smaller list of mentorship eligible employees than the number of employees expected to retire I've created an additional analysis with broader criteria on eligibility for mentorship including anyone born between 1960 and 1969 and then summarzied that in a new table by title for easy visualiztion.  

SELECT DISTINCT ON (emp_no) employees.emp_no, 
	employees.first_name, 
	employees.last_name, 
	employees.birth_date, 
	dept_emp.from_date, 
	dept_emp.to_date, 
	titles.title
INTO mentorship_eligibility_broader
FROM employees
INNER JOIN dept_emp 
ON employees.emp_no = dept_emp.emp_no
INNER JOIN titles
ON employees.emp_no = titles.emp_no
WHERE birth_date BETWEEN '1960-01-01' AND '1969-12-31'
AND dept_emp.to_date = '9999-01-01'
ORDER BY emp_no, to_date DESC;

SELECT COUNT (emp_no), title
INTO mentorship_eligibility_broader_summary
FROM mentorship_eligibility_broader
GROUP BY title
ORDER BY COUNT (emp_no) DESC;

| count | title              |
|-------|--------------------|
| 33069 | Engineer           |
| 31975 | Senior Staff       |
| 9975  | Staff              |
| 9374  | Senior Engineer    |
| 4735  | Assistant Engineer |
| 4618  | Technique Leader   |
| 10    | Manager            |

With this broader criteria we see a pool of employees that is now larger than the pool of retiring employees.  As well hiring from outside the organization is an option and though we have no data on that currently it's an area that should be explored as well by Pewlett Hackard.  
