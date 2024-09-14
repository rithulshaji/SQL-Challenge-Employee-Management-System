# SQL-Challenge-Employee-Management-System
This repository contains SQL scripts for managing employee details, departments, and their respective salaries. It also includes example queries for performing various operations like counting employees, finding top salaries, and more.

## Table of Contents

- [Database Schema](#database-schema)
  - [Employee_Details Table](#employee_details-table)
  - [Departments Table](#departments-table)
  - [Department_Employee Table](#department_employee-table)
- [Data Insertion](#data-insertion)
- [SQL Queries](#sql-queries)
  - [a. Find total number of employees](#a-find-total-number-of-employees)
  - [b. Find total number of employees in every department](#b-find-total-number-of-employees-in-every-department)
  - [c. Employees above age 30](#c-employees-above-age-30)
  - [d. Employees below age 25 with salary more than 30,000](#d-employees-below-age-25-with-salary-more-than-30000)
  - [e. Highest salary for every department](#e-highest-salary-for-every-department)
  - [f. 3rd highest salary in every department](#f-3rd-highest-salary-in-every-department)
  - [g. Top 2 salaried employees in every department](#g-top-2-salaried-employees-in-every-department)
  - [h. Top 2 unique salaries in every department](#h-top-2-unique-salaries-in-every-department)

## Database Schema

### Employee_Details Table

```sql
CREATE TABLE Employee_Details (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    birth_date DATETIME
);

INSERT INTO Employee_Details (id, name, birth_date) VALUES
(1, 'Alice', '1990-01-15'),
(2, 'Bob', '1985-03-22'),
(3, 'Charlie', '1998-07-09'),
(4, 'David', '1999-08-12'),
(5, 'Eve', '1980-11-23'),
(6, 'Frank', '2000-04-18'),
(7, 'Grace', '1995-02-28'),
(8, 'Hannah', '1993-05-05');
```

```sql
CREATE TABLE Departments (
    id INT PRIMARY KEY,
    name VARCHAR(100)
);

INSERT INTO Departments (id, name) VALUES
(1, 'HR'),
(2, 'Finance'),
(3, 'IT'),
(4, 'Marketing');
```

```sql
CREATE TABLE Department_Employee (
    id INT PRIMARY KEY,
    department INT,
    employee_salary INT,
    FOREIGN KEY (department) REFERENCES Departments(id)
);

INSERT INTO Department_Employee (id, department, employee_salary) VALUES
(1, 1, 50000),
(2, 2, 60000),
(3, 3, 45000),
(4, 3, 30000),
(5, 4, 70000),
(6, 1, 55000),
(7, 2, 62000),
(8, 4, 75000);
```
##

### a. Find total number of employees
```sql
SELECT COUNT(*) AS total_employees 
FROM Employee_Details;
```

### b. Find total number of employees in every department
```sql
SELECT d.name AS department_name, COUNT(*) AS no_of_employees 
FROM Departments d 
INNER JOIN Department_Employee de ON d.id = de.department
GROUP BY d.name;
```

### c. Employees above age 30
```sql
SELECT id, name, birth_date, TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) - 
    (DATE_FORMAT(CURDATE(), '%m%d') < DATE_FORMAT(birth_date, '%m%d')) AS Age 
FROM Employee_Details
WHERE TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) - 
    (DATE_FORMAT(CURDATE(), '%m%d') < DATE_FORMAT(birth_date, '%m%d')) > 30;
```

### d. Employees below age 25 with salary more than 30,000
```sql
SELECT e.id, name, birth_date, TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) - 
    (DATE_FORMAT(CURDATE(), '%m%d') < DATE_FORMAT(birth_date, '%m%d')) AS age, employee_salary 
FROM Employee_Details e 
INNER JOIN Department_Employee de ON e.id = de.id
WHERE TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) - 
    (DATE_FORMAT(CURDATE(), '%m%d') < DATE_FORMAT(birth_date, '%m%d')) < 25 
    AND employee_salary > 30000;
```

### e. Highest salary for every department
```sql
SELECT d.name AS department_name, MAX(employee_salary) AS highest_salary
FROM Departments d 
INNER JOIN Department_Employee de ON d.id = de.department
GROUP BY d.name;
```

### f. 3rd highest salary in every department
```sql
WITH ranked_details AS (
  SELECT d.name AS department_name, employee_salary AS salary, 
         DENSE_RANK() OVER(PARTITION BY d.name ORDER BY employee_salary DESC) AS d_rnk 
  FROM Departments d 
  INNER JOIN Department_Employee de ON d.id = de.department
)
SELECT * FROM ranked_details WHERE d_rnk = 3;
```

### g. Top 2 salaried employees in every department
```sql
WITH ranked_details AS (
  SELECT d.name AS department_name, e.name AS employee_name, employee_salary, 
         ROW_NUMBER() OVER(PARTITION BY d.name ORDER BY employee_salary DESC) AS rn
  FROM Departments d 
  INNER JOIN Department_Employee de ON d.id = de.department 
  INNER JOIN Employee_Details e ON de.id = e.id
)
SELECT department_name, employee_name, employee_salary, rn 
FROM ranked_details 
WHERE rn <= 2;
```

### h. Top 2 unique salaries in every department
```sql
WITH ranked_details AS (
  SELECT d.name, employee_salary AS salary, 
         DENSE_RANK() OVER(PARTITION BY d.name ORDER BY employee_salary DESC) AS d_rnk 
  FROM Departments d 
  INNER JOIN Department_Employee de ON d.id = de.department
)
SELECT * FROM ranked_details WHERE d_rnk <= 2;
```

### By Rithul Shaji


