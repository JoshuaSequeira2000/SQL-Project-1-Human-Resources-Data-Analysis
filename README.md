# SQL Project 1 - Exploratory Data Analysis - Human Resources Data Analysis.

## Complete code attached - Human Resources Data Analysis.sql

## Data Insights Using SQL.

### 1) Scalar functions to provide the average salary per department.

#### Using the above function with a select statement.
select *, dbo.Average_Salary_Per_Department(department_id) as Average_Salary_Per_Department from employees\
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/7ab45c35-134e-48f5-b5b2-6c0fccdbc917)

#### Using the above function separately without a select statement.
declare @ReturnValue as int\
execute @ReturnValue = dbo.Average_Salary_Per_Department 3 -- Department ID passed.\
select @ReturnValue as Average_Salary_Per_Department\
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/7567395d-5037-49f2-bbd5-e6d9eae0194a)

### 2) Scalar function to find the total number of employees working in each department.

#### Using the above function with a select statement.
select *, dbo.Total_Employees_Per_Department(department_id) as Total_Employees_Per_Department from departments\
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/409a739c-9ec7-4a85-8b33-e57a984bdc05)

#### Using the above function separately without a select statement.
declare @ReturnValue int\
execute @ReturnValue = dbo.Total_Employees_Per_Department 5\
select @ReturnValue as Total_Employees_Per_Department\
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/ed40fac1-7b55-43ff-947c-68824395b2e7)

### 3) Scalar function to find the number of employees from each country.

#### Using the above function with a select statement.
select *, dbo.Number_Of_Employees_Per_Country(country_id) as Number_Of_Employees_Per_Country from countries\
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/e7843784-631a-4759-b0ef-f137d0bfc7e3)

### Using the above function separately without a select statement.
declare @ReturnValue char(2)\
execute @ReturnValue = dbo.Number_Of_Employees_Per_Country 'US'\
select @ReturnValue as Number_Of_Employees_Per_Country\
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/ecd133fc-3a5d-49bf-a9dd-0742c18b9e05)

### 4) Inline table function to find the number of employees from each region.

#### Running the function.
select * from dbo.Total_Employees_Per_Region(1)\
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/3eb6a8a7-29b6-4913-99da-705a74cebc0c)

### 5) Multi Statement Table Function which segregates the employees based on their salary.

#### Running the function.
select * from dbo.Employee_Salary_Details(100)\
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/85c16497-3860-4a4b-bbd8-ec078730136f)

### 6) Multi Statement Table Function to the employee with the highest salary based on the department_ID. 

#### Running the function.
select * from dbo.Highest_Dept_Salary(7)\
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/2c1e7538-dcee-4a14-92e1-671577715ad7)

### 7) Finding all employees who also have dependents.
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/1abc229a-ecbe-464b-9acd-0c703f5c8155)

### 8) Finding all the remaining employees who do not have dependents.
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/fa55bf3e-0194-49fd-994e-6d25a189c309)

### 9) Finding all employees whose salary is less than 7000, are from either department 2,4,6, and also have dependents.
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/94d73f6d-133f-473c-9a65-45ad24193c5d)

### 10) Query all the employees who also have dependents and whose name starts with 'S'.
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/19f062fb-3d0d-4195-8985-cb114a181c09)

### 11) Find the total number of departments in each location.
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/798545fc-c9b6-4ba0-a65c-26641dff0de0)

### 12) Find the total number of countries in each region.
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/a29c829a-d3af-48b5-9e3f-9c23c6993a44)

### 13) Find the average salary of employees based on their jobs.
#### Using correlated sub-query.
#### Using the cast function to reduce the number of 0's after decimal.
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/9d0abdf3-e2c6-4f76-947f-f2b26210a81a)

### 14) Find the top 5 employees based on their salary - Department Wise.
#### Using WITH clause with a single temporary table.
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/2faf3380-d868-41f4-8540-33fd9bf8b183)

### 15) Find all employees whose salary is greater than or equal to the department's average salary.
#### Using WITH clause.
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/d41f4ccb-ad01-48c7-bba2-6400582f0499)

### 16) Finding the total number of employees hired each month for each year.
#### Using pivot table.
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/5070c208-ecfb-432e-bc3d-b80ea056967f)

### 17) Finding the total number of employees in each department.
#### Using case when, rollup, and grouping.
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/35620d15-7698-4d05-b882-aa157ff4a7e3)

### 18) Segregating the employee's salaries into bucket ranges.
#### Using Ntile() windows function.
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/d7641b48-50d0-49e1-a010-424e75fc591d)

### 19) Finding the highest and lowest salary in each department.
#### Using first_value() and last_value() windows function
![image](https://github.com/JoshuaSequeira2000/SQL-Project-1-Human-Resources-Data-Analysis/assets/92262753/99d17dd4-3247-45af-a73f-50e6866e409a)
