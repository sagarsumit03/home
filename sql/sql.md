# SQL

> Boost Performance of SQL:  
	Do not use the *  operator in your SELECT statements. Instead, use column names.
	Avoid using GROUP BY, ORDER BY, and DISTINCT as much as possible as  SQL Server engine creates a work table and puts the data on the work table
	Select more fields to avoid SELECT DISTINCT
	Create joins with INNER JOIN rather than WHERE
	Use wildcards at the end of a phrase only. SELECT City FROM Customers WHERE City LIKE ‘Char%’ inplace of '%Char%'
	Use EXISTS in place of IN. The Exists keyword evaluates true or false, but IN keyword compare all value in the corresponding sub query column


## Indexes:

The basic syntax of CREATE INDEX is as follows −

	CREATE INDEX index_name ON table_name;


  ### Index Types
PostgreSQL provides several index types: B-tree, Hash, GiST, SP-GiST and GIN.   
Each Index type uses a different algorithm that is best suited to different types of queries. By default, the CREATE INDEX command creates B-tree indexes, which fit the most common situations.

- Single-Column Indexes    
A single-column index is one that is created based on only one table column.

- Multicolumn Indexes  
A multicolumn index is defined on more than one column of a table. 
  
		CREATE INDEX index_name ON table_name (column1_name, column2_name);
- Unique Indexes  
Unique indexes are used not only for performance, but also for data integrity. A unique index does not allow any duplicate values to be inserted into the table.

		CREATE UNIQUE INDEX index_name on table_name (column_name);

- Partial Indexes  
A partial index is an index built over a subset of a table; the subset is defined by a conditional expression.

  		CREATE INDEX index_name on table_name (conditional_expression);
- Implicit Indexes  
Implicit indexes are indexes that are automatically created by the database server when an object is created. Indexes are automatically created for primary key constraints and unique constraints.
	

## create Table:

    CREATE TABLE Employee(
        id number NOT NULL PRIMARY KEY,
        name varchar(20),
        dept number,
        Salary INTEGER, ManagerId INTEGER);

    CREATE INDEX emp 	 /* Create Index */
    ON Employee (id)

## Select All

    select * from Employee;

## Insert Data

    INSERT INTO Employee (id,name,dept,Salary,ManagerId) VALUES ( 11,'Geet',Marketing,20000,NULL);  
    INSERT INTO Employee (id,name,dept,Salary,ManagerId) VALUES ( 13,'Sumit',IT,300000,NULL);  
    INSERT INTO Employee (id,name,dept,Salary,ManagerId) VALUES ( 1,'Sagar',IT,250000,13);  
    INSERT INTO Employee (id,name,dept,Salary,ManagerId) VALUES ( 4,'Roohi',Support,45000,NULL);  
    INSERT INTO Employee (id,name,dept,Salary,ManagerId) VALUES ( 3,'Priyanka',Support,50000,4);  
    INSERT INTO Employee (id,name,dept,Salary,ManagerId) VALUES ( 5,'Yogesh',,100000,NULL);  
    INSERT INTO Employee (id,name,dept,Salary,ManagerId) VALUES ( 6,'Prahlad',HR,220000,NULL);  
    INSERT INTO Employee (id,name,dept,Salary,ManagerId) VALUES ( 7,'Varun',HR,150000,6);  
    INSERT INTO Employee (id,name,dept,Salary,ManagerId) VALUES ( 8,'Satish',Admin,120000,9);    
    INSERT INTO Employee (id,name,dept,Salary,ManagerId) VALUES ( 9,'Hemant',Admin,100000,NULL);  
    INSERT INTO Employee (id,name,dept,Salary,ManagerId) VALUES ( 10,'Shailendra',Marketing,30000,12);  
    INSERT INTO Employee (id,name,dept,Salary,ManagerId) VALUES ( 12,'Geet',Marketing,70000,NULL);


## Duplicate Entry

    select name, count(name) from Employee group by name having count(name) >1;
    
## Delete the duplicate entry

    with cte as
    (select b.id from Person a , Person b where a.Email = b.Email and a.Id < b.Id)

    delete from Person where
    Id in (select * from cte)

## Join

    select d.name from Department d inner join Employee e on d.name = e.dept; 

## Create Index

    /* Create Index */CREATE INDEX emp ON Employee (id)

## Nth Salary

    SELECT salary FROM Employee ORDER BY id DESC LIMIT 5-1, 1;
    
    SELECT TOP 1 salary FROM (SELECT TOP 3 salary FROM employees ORDER BY salary DESC) AS emp ORDER BY salary ASC;

## Employees With Same Dept

    select e1.name, e1.dept from Employee e1, Employee e2 where e1.name != e2.name and e1.dept = e2.dept and e1.dept = 'IT';

## Employee with Highest Salary in Each Dept

    select e1.dept, e1.name, e1.salary as Max_Salary from ( select max(salary) as totalSalary, dept from Employee group by dept) as TEMP inner join Employee e1 where e1.dept = TEMP.dept and e1.salary = TEMP.totalSalary; ----------- not working(priyanka)
    
    REPLACED WITH:
    SELECT e1.dept, e1.name, e1.salary FROM Employee e1 WHERE e1.salary IN (SELECT max(salary) AS maxSalary From Employee GROUP BY dept)

## Employee who are also Managers

    select e1.id, e1.name from Employee e1 join Employee e2 ON e1.id = e2.ManagerId and e1.name != e2.name;
