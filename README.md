# SQL Project 1 - Exploratory Data Analysis - Human Resources Database Analysis.

-- Selecting the database.
use [HR Database]
GO

-- Select statements for all the tables.
select * from [dbo].[countries]
select * from [dbo].[departments]
select * from [dbo].[dependents]
select * from [dbo].[employees]
select * from [dbo].[jobs]
select * from [dbo].[locations]
select * from [dbo].[regions]
GO

-- Functions (Scalar, Inline, Multi-Table)
-- 1) Scalar function to provide the average salary per department.
create function Average_Salary_Per_Department(@DepartmentID int)
returns int
begin
	declare @ReturnValue int
	select @ReturnValue = AVG(salary) from employees e
	inner join departments d on e.department_id = d.department_id
	where @DepartmentID = d.department_id
	return @ReturnValue
end
GO

-- Using the above function with a select statement.
select *, dbo.Average_Salary_Per_Department(department_id) as Average_Salary_Per_Department from employees
GO

-- Using the above function separately without a select statement.
declare @ReturnValue as int
execute @ReturnValue = dbo.Average_Salary_Per_Department 3
select @ReturnValue as Average_Salary_Per_Department
GO

-- 2) Scalar function to find the total number of employees working in each department.
create function Total_Employees_Per_Department(@DepartmentID int)
returns int
begin
	declare @ReturnValue int
	select @ReturnValue = COUNT(e.employee_id) from employees e
	inner join departments d on e.department_id = d.department_id
	where @DepartmentID = d.department_id
	return @ReturnValue
end
GO

-- Using the above function with a select statement.
select *, dbo.Total_Employees_Per_Department(department_id) as Total_Employees_Per_Department from departments

-- Using the above function separately without a select statement.
declare @ReturnValue int
execute @ReturnValue = dbo.Total_Employees_Per_Department 5
select @ReturnValue as Total_Employees_Per_Department
GO

-- 3) Scalar function to find the number of employees from each country.
create function Number_Of_Employees_Per_Country(@CountryID char(2))
returns char(2)
begin
	declare @ReturnValue char(2)
		select @ReturnValue = COUNT(e.employee_id) from employees e 
		left join departments d on e.department_id = d.department_id
		left join locations l on d.location_id = l.location_id
		left join countries c on l.country_id = c.country_id
		where @CountryID = c.country_id
		return @ReturnValue
end
GO

-- Using the above function with a select statement.
select *, dbo.Number_Of_Employees_Per_Country(country_id) as Number_Of_Employees_Per_Country from countries
GO

-- Using the above function separately without a select statement.
declare @ReturnValue char(2)
execute @ReturnValue = dbo.Number_Of_Employees_Per_Country 'US'
select @ReturnValue as Number_Of_Employees_Per_Country
GO

-- 4) Inline table function to find the number of employees from each region.
create function Total_Employees_Per_Region(@RegionID tinyint)
returns table as return
	(select count(e.employee_id) as Total_Number_Of_Employees, r.region_id, r.region_name from employees e 
	 inner join departments d on e.department_id = d.department_id inner join locations l
	 on l.location_id = d.location_id inner join countries c on c.country_id = l.country_id inner join regions r on
	 r.region_id = c.region_id
	 where r.region_id = @RegionID
	 group by r.region_id, r.region_name)
GO

-- Running the function.
select * from dbo.Total_Employees_Per_Region(1)
GO

-- 5) Multi Statement Table Function which segregates the employees based on their salary.
create function Employee_Salary_Details(@EmployeeID int)
returns @ReturnValue table
(Employee_ID smallint, Employee_Salary int, Employee_Designation varchar(15))
as
begin
	if exists(select * from employees where @EmployeeID = employee_id)
		begin
			insert into @ReturnValue(Employee_ID, Employee_Salary, Employee_Designation)
			select employee_id, salary, case when salary < 3000 then 'Intern' 
											 when salary between 3000 and 6000 then 'Associate'
											 when salary between 6001 and 12000 then 'Manager'
											 when salary between 12001 and 18000 then 'Senior Manager'
											 when salary > 18000 then 'Team Lead' end Employee_Designation
											 from employees
			where @EmployeeID = employee_id
		end
	else
		begin
			insert into @ReturnValue(Employee_ID, Employee_Salary, Employee_Designation)
			select 0, 0, 0
		end
	return
end
GO

-- Running the function.
select * from dbo.Employee_Salary_Details(100)
GO

-- 6) Multi Statement Table Function to the employee with the highest salary based on the department_ID. 
create function Highest_Dept_Salary(@DepartmentID tinyint)
returns @ReturnValue table
(Employee_ID int, Employee_First_Name varchar(50), Employee_Last_Name varchar(50), Salary int, Salary_Rank tinyint,
 Department_Name varchar(20), Department_ID tinyint)
as
begin
	if exists(select * from departments where department_id = @DepartmentID)
		begin
			insert into @ReturnValue(Employee_ID, Employee_First_Name, Employee_Last_Name, Salary, Salary_Rank, 
									 Department_Name, Department_ID)
			select table1.* from
			(select e.employee_id, e.first_name, e.last_name, e.salary,
			RANK() over(partition by e.department_id order by e.salary desc) as Salary_Rank,
			d.department_name, d.department_ID
			from employees e inner join departments d on e.department_id = d.department_id
			where e.department_id = @DepartmentID) table1 -- used sub query as I could not use window function in where clause to filter for rank 1 salary.
			where table1.Salary_Rank = 1
		end
	else
		begin
			insert into @ReturnValue(Employee_ID, Employee_First_Name, Employee_Last_Name, Salary, Salary_Rank, 
									 Department_Name, Department_ID)
			select 0, 0, 0, 0, 0, 0, 0
		end
	return
end
GO

-- Running the function.
select * from dbo.Highest_Dept_Salary(7)
GO


-- 7) Finding all employees who also have dependents.
-- Using Sub Query - Where In
select * from employees where employee_id in (select employee_id from dependents)
GO

-- 8) Finding all the remaining employees who do not have dependents.
-- using Sub Query - Where Not In
select * from employees where employee_id not in (select employee_id from dependents)
GO

-- 9) Finding all employees who's salary is less than 7000, is from either department 2,4,6 and also have dependents.
-- Using Sub Query - Where In
select * from employees 
where salary > 7000 and department_id in (2, 4, 6) and employee_id in (select employee_id from dependents)
GO

-- 10) Query all the employees who also have dependents and who's name starts with 'S'.
-- Using Sub Query - From
select * from employees e
inner join (select employee_id from dependents) d
on e.employee_id = d.employee_id
where e.first_name like 'S%'
order by e.employee_id
GO

-- 11) Find the total number of departments in each location.
-- Using correlated sub query. 
select *, (select COUNT(d.department_id) from departments d where d.location_id = l.location_id) as No_Of_Depts
from locations l
order by location_id
GO

-- 12) Find the total number of countries in each region.
-- Using correlated sub query.
select *, (select COUNT(country_id) from countries c where c.region_id = r.region_id) as No_Of_Countries
from regions r
order by region_id
GO

-- 13) Find the average salary of employees based on their jobs.
-- Using correlated sub query.
-- Using cast function to reduce the number of 0's after decimal.
select *, cast((select AVG(salary) from employees e1 where e1.job_id = e2.job_id) as decimal(8,2)) as Avg_Job_Salary
from employees e2
GO

-- 14) Find top 5 employees based on their salary - Department Wise.
-- Using WITH clause with a single temporary table.
with Employee_Salary_Ranking as 
	(select e.employee_id, e.first_name, e.last_name, e.salary, d.department_name,
	 RANK() over(partition by d.department_id order by e.salary desc) as Salary_Ranking
	 from employees e inner join departments d 
	 on e.department_id = d.department_id)
select * from Employee_Salary_Ranking
where Salary_Ranking <=5
GO

-- 15) Find all employees who's salary is greater than or equal to the department average salary.
-- Using WITH clause.
with Employee_Department_Salary as

	(select e.employee_id, e.first_name, e.last_name, d.department_id, d.department_name, e.salary,
	 cast(AVG(salary) over(partition by d.department_id order by (select null)) as decimal(9,2))
	 as Avg_Dept_salary,

	 case when e.salary >= cast(AVG(salary) over(partition by d.department_id order by (select null)) as decimal(9,2))
	 then 'Salary higher than or equal to dept avg' else 'Salary lower than dept avg' end as Result
	 from employees e 

	 inner join departments d on e.department_id = d.department_id)

select * from Employee_Department_Salary 
where Result = 'Salary higher than or equal to dept avg'
order by department_id
GO

-- 16) Finding the total number of employees hired each month for each year.
-- Using pivot table.
with Employee_Hire_Count as
	(select YEAR(hire_date) as Hire_Year, MONTH(hire_date) as Hire_Month, employee_id from employees)

select Hire_Year, [1] as January, [2] as February, [3] as March, [4] as April, [5] as May, [6] as June, [7] as July, 
				  [8] as August, [9] as September, [10] as October, [11] as November, [12] as December
from Employee_Hire_Count
pivot(COUNT(employee_id) for Hire_Month in ([1], [2], [3], [4], [5], [6], [7], [8], [9], [10], [11], [12])) as pvt
order by Hire_Year desc
GO

-- 17) Finding the total number of employees in each department.
-- Using case when, rollup and grouping.
select case when d.department_name is null then 'TOTAL' else d.department_name end Department_Name,
COUNT(e.employee_id) as Number_Of_Employees, GROUPING(d.department_name) as Is_Grouped from employees e 
inner join departments d on e.department_id = d.department_id
group by rollup(d.department_name)
order by d.department_name desc
GO

-- 18) Segregating the employees salaries into bucket ranges.
select *, NTILE(5) over(partition by department_id order by salary desc) as Buckets,
case when NTILE(5) over(partition by department_id order by salary desc) between 1 and 3 then 'Upper End Salary'
else 'Lower End Salary' end as Result
from employees
GO

-- 19) Finding the highest and lowest salary in each department.
select e.employee_id, e.first_name, e.last_name, e.salary, d.department_name ,
FIRST_VALUE(e.salary) over(partition by d.department_name order by e.salary) as Lowest_Salary,
LAST_VALUE(e.salary) over(partition by d.department_name order by e.salary 
						  rows between unbounded preceding and unbounded following) as Highest_Salary
from employees e inner join departments d on e.department_id = d.department_id
GO
