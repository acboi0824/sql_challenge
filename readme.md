# SQL Challenge - Employees Database
It’s a beautiful spring day, and it’s been two weeks since you were hired as a new data engineer at Pewlett Hackard. Your first major task is a research project on employees of the corporation from the 1980s and 1990s. All that remains of the database of employees from that period are six CSV files.

In this assignment, you will design the tables to hold data in the CSVs, import the CSVs into a SQL database, and answer questions about the data. In other words, you will perform **data modeling**, **data engineering**, and **data analysis**.

## 1. Data Modeling
Using a basic modeling technique called Entity-Relationship Diagrams (ERD) and a free tool called quickdatabasediagrams.com, create the database tables given from the spreadsheets. These tables are  `employees`,`salaries`,`titles`,`dept_emp`,`dept_managers`, and `departments`. The ER diagram is used to visualize the tables' relationships among each other and how they are linked by primary and foreign keys.

The ERD image:

![erd](https://github.com/acboi0824/sql_challenge/blob/main/images/emp_db%20schema%20diagram.png)

## 2. Data Engineering
Using the provided information, create a table schema for each of the six CSV files. Specifying the data types, primary keys, foreign keys, and other constraints.

Click the following link to see the schema file: 
[Employees Schema](https://github.com/acboi0824/sql_challenge/blob/main/SQL%20and%20Bonus/emp_db%20schema%20code.sql)

## 3. Data Analysis
After the import process, an analysis was completed in POSTGRESQL from the database. The full code can be found on this link: [Full Data Analysis Code](https://github.com/acboi0824/sql_challenge/blob/main/SQL%20and%20Bonus/data_analysis_sql.sql)

1. List the following details of each employee: employee number, last name, first name, sex, and salary.
```
select e.emp_no, e.last_name, e.first_name, e.sex, s.salary
from employees e
left join salaries s on e.emp_no = s.emp_no
order by e.emp_no asc
;
```
2. List first name, last name, and hire date for employees who were hired in 1986.
```
select first_name, last_name, hire_date
from employees
where date_part('year', hire_date) = 1986
order by hire_date asc
;
```
3. List the manager of each department with the following information: department number, department name, the manager's employee number, last name, first name.
```
select dm.dept_no, d.dept_name, dm.emp_no, e.first_name, e.last_name
from dept_manager dm
left join employees e on dm.emp_no = e.emp_no
left join departments d on dm.dept_no = d.dept_no
;
```
4. List the department of each employee with the following information: employee number, last name, first name, and department name.
```
select e.emp_no, e.last_name, e.first_name, d.dept_name
from employees e
left join dept_emp de on e.emp_no = de.emp_no
left join departments d on d.dept_no = de.dept_no
;
```
5. List first name, last name, and sex for employees whose first name is "Hercules" and last names begin with "B."
```
select first_name, last_name, sex
from employees
where first_name = 'Hercules' and last_name like 'B%'
;
```
6. List all employees in the Sales department, including their employee number, last name, first name, and department name.
```
select e.emp_no, e.last_name, e.first_name, d.dept_name
from employees e
left join dept_emp de on e.emp_no = de.emp_no
left join departments d on de.dept_no = d.dept_no
where d.dept_name = 'Sales'
```
7. List all employees in the Sales and Development departments, including their employee number, last name, first name, and department name.
```
select e.emp_no, e.last_name, e.first_name, d.dept_name
from employees e
left join dept_emp de on e.emp_no = de.emp_no
left join departments d on de.dept_no = d.dept_no
where d.dept_name = 'Sales' or d.dept_name = 'Development'
order by e.emp_no
;
```
8. List the frequency count of employee last names (i.e., how many employees share each last name) in descending order.
```
select last_name, count(last_name) as frequency
from employees
group by last_name
order by frequency desc
;
```
## Bonus
As you examine the data, you begin to suspect that the dataset is fake. Maybe your boss gave you spurious data in order to test the data engineering skills of a new employee? To confirm your hunch, you decide to create a visualization of the data to present to your boss. Follow these steps: 

### 1. Import the SQL database into Pandas.
```
# Get the username and password from config file
from config import username, password

# Create engine and connection to employees db
engine = create_engine(f'postgresql://{username}:{password}@localhost:5432/employees_db')
conn = engine.connect()

# Query All Records in the the Database
emp_table = pd.read_sql("SELECT * FROM employees", conn)
salary_table = pd.read_sql("SELECT * FROM salaries", conn)
titles_table = pd.read_sql("SELECT * FROM titles", conn)
```

### 2. Create a histogram to visualize the most common salary ranges for employees.
![histogram_employees_by_salary](https://github.com/acboi0824/sql_challenge/blob/main/images/employee_salary_distribution.png)

### 3. Create a bar chart of average salary by title.
![bar chart_average_salary_title](https://github.com/acboi0824/sql_challenge/blob/main/images/average_salary_by_title.png)
