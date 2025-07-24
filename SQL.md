# SQL

## create Table:

    CREATE TABLE Employee(
        id number NOT NULL PRIMARY KEY,
        name varchar(20),
        dept number,
        Salary INTEGER, ManagerId INTEGER);

    CREATE INDEX emp ON Employee (id);

## Select All

    select * from Employee;

## Insert Data

    ```
    INSERT INTO Employee (id, name, dept, Salary, ManagerId) VALUES (11, 'Geet', 'Marketing', 20000, NULL);
    INSERT INTO Employee (id, name, dept, Salary, ManagerId) VALUES (13, 'Sumit', 'IT', 300000, NULL);
    INSERT INTO Employee (id, name, dept, Salary, ManagerId) VALUES (1, 'Sagar', 'IT', 250000, 13);
    INSERT INTO Employee (id, name, dept, Salary, ManagerId) VALUES (4, 'Roohi', 'Support', 45000, NULL);
    INSERT INTO Employee (id, name, dept, Salary, ManagerId) VALUES (3, 'Priyanka', 'Support', 50000, 4);
    INSERT INTO Employee (id, name, dept, Salary, ManagerId) VALUES (5, 'Yogesh', NULL, 100000, NULL);
    INSERT INTO Employee (id, name, dept, Salary, ManagerId) VALUES (6, 'Prahlad', 'HR', 220000, NULL);
    INSERT INTO Employee (id, name, dept, Salary, ManagerId) VALUES (7, 'Varun', 'HR', 150000, 6);
    INSERT INTO Employee (id, name, dept, Salary, ManagerId) VALUES (8, 'Satish', 'Admin', 120000, 9);
    INSERT INTO Employee (id, name, dept, Salary, ManagerId) VALUES (9, 'Hemant', 'Admin', 100000, NULL);
    INSERT INTO Employee (id, name, dept, Salary, ManagerId) VALUES (10, 'Shailendra', 'Marketing', 30000, 12);
    INSERT INTO Employee (id, name, dept, Salary, ManagerId) VALUES (12, 'Geet', 'Marketing', 70000, NULL);

    ```


## Find Duplicate Names

    select name, count(name) from Employee group by name having count(name) >1;
    
## Delete the duplicate entry: 
    to get duplicate : Where name is same but id is not same. 
    
    select distinct(a.name) from Employee a, Employee b where a.name = b.name and a.id <> b.id;

## Join

    select d.name from Department d inner join Employee e on d.name = e.dept; 


## Nth Salary

    select min(Salary) from Employee where Salary in (select Salary from Employee order by Salary desc limit 3);
   

## Employees With Same Dept

    select e1.name, e1.dept from Employee e1, Employee e2 where e1.name != e2.name and e1.dept = e2.dept;

## Find 3rd and 4th Highest Salary:
offset : used to skip a specified number of rows in a query result. here it skips 1st and 2nd rows.

    SELECT Salary FROM Employee ORDER BY Salary DESC LIMIT 2 OFFSET 2;

## No of Empoyees in each Department:

    select dept, count(*) from Employee group by dept;


## Employee with Highest Salary in Each Dept

    MAX Salary in Each Department:
    
    select  Max(Salary), dept from Employee group by dept;
    
    SELECT dept, name, Salary FROM Employee WHERE (dept,Salary) IN (SELECT dept, MAX(Salary) FROM Employee GROUP BY dept)

## Employee who are also Managers

    select e1.id, e1.name from Employee e1 join Employee e2 ON e1.id = e2.ManagerId and e1.name != e2.name;
