### SLIP 01 

Consider the following Bank database which maintains information about its 

branches, customers and 

their loan applications. 

Branch (Bid integer, brname varchar (30), brcity varchar (10)) 

Customer (Cno integer, cname varchar (20), caddr varchar (35), city varchar 

(15)) 

Loan\_application (Lno integer, l\_amt\_required money, lamtapproved money, 

l\_date date) 

Relationship: 

Branch, Customer, Loan\_application are related with ternary relationship as 

follows: 

Ternary (Bid, Cno, Lno ) 

Constraints: Primary key, l\_amt\_required should be greater than zero. 

Using above database solve following Queries: 

1\. Display sum of loan amount approved branch wise from 1st June 2019 to 1st 

June 2020. 

2\. Display customers details of ‘Aundh’ branch. 

3\. Display total of loan amount required. 

4\. Display all details of Branch table 

SOLUTION 

Create Tables 

Branch Table 

CREATE TABLE Branch ( 

Bid INTEGER PRIMARY KEY, 

brname VARCHAR(30), 

brcity VARCHAR(10) 

); 

Customer Table 

CREATE TABLE Customer ( 

Cno INTEGER PRIMARY KEY, 

cname VARCHAR(20), 

caddr VARCHAR(35), 

city VARCHAR(15) 

); 

Loan\_application Table 

Constraint: loan amount required must be greater than 

zero 

CREATE TABLE Loan\_application ( 

Lno INTEGER PRIMARY KEY, 

l\_amt\_required MONEY , 

l\_amtapproved MONEY, 

l\_date DATE 

CONSTRAINT loan\_amt\_check CHECK 

(l\_amt\_required::numeric > 0) 

); 

Ternary Relationship Table 

This table connects Branch, Customer and 

Loan\_application 

CREATE TABLE Ternary ( 

Bid INTEGER, 

Cno INTEGER, 

Lno INTEGER, 

FOREIGN KEY (Bid) REFERENCES Branch(Bid), 

FOREIGN KEY (Cno) REFERENCES Customer(Cno), 

FOREIGN KEY (Lno) REFERENCES Loan\_application(Lno) 

); 

INSERT INTO Branch VALUES 

(1,'Aundh','Pune'), 

(2,'Shivajinagar','Pune'), 

(3,'Hadapsar','Pune'); 

INSERT 0 3 

INSERT INTO Customer VALUES 

(101,'Rahul','Karve Nagar','Pune'), 

(102,'Sneha','Aundh Road','Pune'), 

(103,'Amit','Hadapsar','Pune'); 

INSERT 0 3 

INSERT INTO Loan\_application VALUES 

(1001,50000,45000,'2019-07-10'), 

(1002,70000,65000,'2020-02-15'), 

(1003,40000,35000,'2019-08-25'); 

INSERT 0 3 

INSERT INTO Ternary VALUES 

(1,101,1001), 

(2,102,1002), 

(1,103,1003); 

INSERT 0 3 

Query 1 : Display sum of loan amount approved branch 

wise from 1st June 2019 to 1st June 2020. 

select brname,sum(l\_amtapproved) as 

total\_Loan\_Approved  

from branch,ternary,loan\_application  

where l\_date between '2019-06-01' and '2020-06-01'  

and branch.bid=ternary.bid  

and loan\_application.Lno=ternary.Lno  

group by brname; 

Query 2. Display customers details of ‘Aundh’ branch. 

select customer.cno,cname,city,brname,brcity  

from customer,ternary,branch  

where brname = 'Aundh'  

and ternary.Cno = customer.Cno  

and branch.Bid = ternary.Bid; 

Query 3. Display total of loan amount required. 

SELECT SUM(l\_amt\_required) AS total\_loan\_required 

FROM Loan\_application; 

Query 4. Display all details of Branch table. 

SELECT \* FROM Branch; 

Q.2  

1\. Write a trigger before insert record of customer. If 

the customer number is less than or 

equal to zero and customer name is null then give the 

appropriate message 

CREATE OR REPLACE FUNCTION check\_customer() 

RETURNS TRIGGER 

LANGUAGE plpgsql 

AS $$ 

BEGIN 

IF NEW.Cno <= 0 AND NEW.cname IS NULL THEN 

RAISE EXCEPTION 'Customer number must be greater 

than 0 and customer name cannot be NULL'; 

END IF; 

RETURN NEW; 

END; 

$$; 

CREATE TRIGGER trg\_check\_customer 

BEFORE INSERT 

ON Customer 

FOR EACH ROW 

EXECUTE FUNCTION check\_customer(); 

Output :  

postgres=# \\! vi s1.sql 

postgres=# \\i s1.sql 

CREATE FUNCTION 

CREATE TRIGGER 

postgres=# INSERT INTO Customer VALUES (0, NULL, 

'Pune', 'Pune'); 

ERROR:  Customer number must be greater than 0 and 

customer name cannot be NULL 

CONTEXT:  PL/pgSQL function check\_customer() line 4 at 

RAISE 

2\. Write a stored function to accept customer name as 

an input parameter and display 

loan information of that customer. 

CREATE OR REPLACE FUNCTION get\_customer\_loan(cust\_name 

VARCHAR) 

RETURNS TABLE( 

&#x20;cname VARCHAR, 

&#x20;lno INTEGER, 

&#x20;l\_amt\_required MONEY, 

&#x20;l\_amtapproved MONEY, 

&#x20;l\_date DATE 

) 

LANGUAGE plpgsql 

AS $$ 

BEGIN 

&#x20;RETURN QUERY  

&#x20;SELECT c.cname, 

&#x20;       l.Lno, 

&#x20;       l.l\_amt\_required, 

&#x20;       l.l\_amtapproved, 

&#x20;       l.l\_date 

&#x20;FROM Customer c, Ternary t,Loan\_application l 

&#x20;where  c.Cno = t.Cno 

&#x20;and t.Lno = l.Lno 

&#x20;and c.cname = cust\_name; 

&#x20;   

END; 

$$; 

&#x20;

Output : 

&#x20;

postgres=# \\! vi s2.sql 

postgres=# \\i s2.sql 

CREATE FUNCTION 

postgres=# SELECT \* FROM get\_customer\_loan('Rahul'); 

&#x20;











&#x20;

### **SLIP 02** 

Consider the following Bank database which maintains information about its branches, 

customers and 

their loan applications. 

Branch (Bid integer, brname varchar (30), brcity varchar (10)) 

Customer (Cno integer, cname varchar (20), caddr varchar (35), city varchar (15)) 

Loan\_application (Lno integer, l\_amt\_required money, lamtapproved money, l\_date date) 

Relationship: 

Branch, Customer, Loan\_application are related with ternary relationship as follows: 

Ternary (Bid, Cno, Lno ) 

Constraints: Primary key, l\_amt\_required should be greater than zero. 

Using above database solve following Queries: 

1\. Insert 5 records into Branch table. 

2\. Display loan details from the ‘Pimpri’ branch. 

3\. Display name of customer whose name start by letter ‘A’. 

4\. Display customer details who have applied for a loan of 8, 00,000. 

SOLUTION 

Create Tables 

Branch Table 

CREATE TABLE Branch ( 

Bid INTEGER PRIMARY KEY, 

brname VARCHAR(30), 

brcity VARCHAR(10) 

); 

Customer Table 

CREATE TABLE Customer ( 

Cno INTEGER PRIMARY KEY, 

cname VARCHAR(20), 

caddr VARCHAR(35), 

city VARCHAR(15) 

); 

Loan\_application Table 

Constraint: loan amount required must be greater than zero 

CREATE TABLE Loan\_application ( 

Lno INTEGER PRIMARY KEY, 

l\_amt\_required MONEY , 

l\_amtapproved MONEY, 

l\_date DATE 

CONSTRAINT loan\_amt\_check CHECK (l\_amt\_required::numeric > 0) 

); 

Ternary Relationship Table 

This table connects Branch, Customer and Loan\_application 

CREATE TABLE Ternary ( 

Bid INTEGER, 

Cno INTEGER, 

Lno INTEGER, 

FOREIGN KEY (Bid) REFERENCES Branch(Bid), 

FOREIGN KEY (Cno) REFERENCES Customer(Cno), 

FOREIGN KEY (Lno) REFERENCES Loan\_application(Lno) 

); 

Using above database solve following Queries: 

Query 1. Insert 5 records into Branch table. 

INSERT INTO Branch VALUES (1,'Aundh','Pune'); 

INSERT INTO Branch VALUES (2,'Shivajinagar','Pune'); 

INSERT INTO Branch VALUES (3,'Hadapsar','Pune'); 

INSERT INTO Branch VALUES (4,'Kothrud','Pune'); 

INSERT INTO Branch VALUES (5,'Baner','Pune'); 

SELECT \* FROM Branch; 

Query2. Display loan details from the ‘Pimpri’ branch. 

Select \* from Loan\_application,Ternary,Branch  

where brname='Pimpri'  

and Branch.Bid=Ternary.Bid  

and Loan\_application.Lno=Ternary.Lno 

Query3. Display name of customer whose name start by letter ‘A’. 

SELECT cname 

FROM Customer 

WHERE cname LIKE 'A%'; 

Query4. Display customer details who have applied for a loan of 

8, 00,000. 

SELECT C.Cno, C.cname 

FROM Customer C, Ternary T, Loan\_application LA 

WHERE LA.l\_amt\_required = 800000 

AND LA.Lno = T.Lno 

AND C.Cno = T.Cno; 

Q.2) Using above database solve following questions: 

1\. Create a view to list all customer detail of ‘Pimpri’ branch. 

CREATE VIEW pimpri\_customer\_view AS 

SELECT C.\* 

FROM Customer C, Ternary T, Branch B 

WHERE C.Cno = T.Cno 

AND B.Bid = T.Bid 

AND B.brname = 'Pimpri'; 

select \* from pimpri\_customer\_view; 

Query 2. Write a stored function to count number of customers of 

particular branch. (Accept branch 

name as an input parameter). Display message for invalid branch 

name. 

CREATE OR REPLACE FUNCTION count\_customers(br\_name VARCHAR) 

RETURNS INTEGER 

LANGUAGE plpgsql 

AS $$ 

DECLARE 

total INTEGER; 

BEGIN 

SELECT COUNT(C.Cno) INTO total 

FROM Customer C, Ternary T, Branch B 

WHERE C.Cno = T.Cno 

AND B.Bid = T.Bid 

AND B.brname = br\_name; 

IF total = 0 THEN 

RAISE NOTICE 'Invalid branch name or no customers found'; 

END IF; 

RETURN total; 

END; 

$$; 

Output :  

postgres=# \\! vi s3.sql 

postgres=# \\i s3.sql 

CREATE FUNCTION 

postgres=# SELECT count\_customers('Pimpri'); 





### SLIP 03  

Q.1) Create the following database in 3NF using PostgresSQL. 

Consider the following database of Student Competition 

Student (Sid,sname,class) 

Competition (cno,cname, ctype) 

\[Total Marks: 10] 

Relationship: 

Student and Competition related with many to many relationship 

with attribute rank and year. 

Constraints: Primary key sid,cno. 

Using above database solve following Queries: 

1\. Display Student id and name. 

2\. List the name of student in sorting order. 

3\. List the name of students who took part in national level 

competition. 

4\. List the competition details.. 

Q.2) Using above database solve following questions: 

1\. Write a trigger before inserting record of student in student 

table. If the student id is 

less than or equal to zero then display the appropriate error 

message. 

Student Table 

CREATE TABLE Student ( 

Sid INTEGER PRIMARY KEY, 

sname VARCHAR(30), 

class VARCHAR(10) 

); 

Competition Table 

CREATE TABLE Competition ( 

cno INTEGER PRIMARY KEY, 

cname VARCHAR(30), 

ctype VARCHAR(20) 

); 

Participation Table (Relationship Table) 

CREATE TABLE Participation ( 

Sid INTEGER, 

cno INTEGER, 

rank INTEGER, 

year INTEGER, 

FOREIGN KEY (Sid) REFERENCES Student(Sid), 

FOREIGN KEY (cno) REFERENCES Competition(cno) 

); 

INSERT INTO Student VALUES 

(1,'Amit','SYBCA'), 

(2,'Anjali','SYBCA'), 

(3,'Rohit','TYBCA'), 

(4,'Sneha','FYBCA'), 

(5,'Akash','TYBCA'); 

INSERT INTO Competition VALUES 

(101,'Coding Contest','National'), 

(102,'Quiz Competition','State'), 

(103,'Hackathon','National'), 

(104,'Debugging Contest','College'), 

(105,'Project Exhibition','State'); 

INSERT INTO Participation VALUES 

(1,101,1,2023), 

(2,102,2,2023), 

(3,103,3,2022), 

(4,104,1,2023), 

(5,105,2,2022); 

SELECT \* FROM Student; 

SELECT \* FROM Competition; 

SELECT \* FROM Participation; 

Query 1. Display Student id and name. 

SELECT Sid, sname 

FROM Student; 

Query 2. List the name of student in sorting order. 

SELECT sname 

FROM Student 

ORDER BY sname; 

Query 3. List the name of students who took part in national 

level competition. 

SELECT S.sname 

FROM Student S, Participation P, Competition C 

WHERE S.Sid = P.Sid 

AND C.cno = P.cno 

AND C.ctype = 'National'; 

Query 4. List the competition details.. 

SELECT \* 

FROM Competition; 

Q.2) Using above database solve following questions: 

1\. Write a trigger before inserting record of student in student 

table. If the student id is 

less than or equal to zero then display the appropriate error 

message. 

CREATE OR REPLACE FUNCTION check\_student\_id() 

RETURNS TRIGGER 

LANGUAGE plpgsql 

AS $$ 

BEGIN 

IF NEW.Sid <= 0 THEN 

RAISE EXCEPTION 'Student ID must be greater than 0'; 

END IF; 

RETURN NEW; 

END; 

$$; 

CREATE TRIGGER trg\_check\_student 

BEFORE INSERT 

ON Student 

FOR EACH ROW 

EXECUTE FUNCTION check\_student\_id(); 

OUTPUT : 

postgres=# \\! vi s4t.sql 

postgres=# \\i s4t.sql 

CREATE FUNCTION 

CREATE TRIGGER 

postgres=# INSERT INTO Student VALUES (0,'Amit','SYBCA'); 

ERROR:  Student ID must be greater than 0 

CONTEXT:  PL/pgSQL function check\_student\_id() line 4 at RAISE 

2\. Create a View to display student details along with their 

Competition name. 

CREATE VIEW student\_competition\_view AS 

SELECT S.Sid, S.sname, S.class, C.cname 

FROM Student S, Participation P, Competition C 

WHERE S.Sid = P.Sid 

AND C.cno = P.cno; 

SELECT \* FROM student\_competition\_view; 







### SLIP 04 

Q1) Create the following database in 3NF using PostgresSQL. 

\[Total Marks: 10] 

Consider the following database of Bus-Transport System. Many 

buses run on one route. Drivers 

are allotted to buses shift-wise. 

Bus 

(Bus\_no int , capacity int , depot\_name varchar (20)) 

Route (Route\_no int, source varchar (20), destination varchar 

(20), no\_of\_stations int) 

Driver (Driver\_no int, driver\_name varchar (20), license\_no int, 

address varchar 

(20),age int , salary float) 

Relationship: 

Bus and Route related with many to one relationship. 

Bus and Driver related with many to many relationship with 

descriptive attributes, Shift – it can 

be 1 (Morning) or 2 (Evening) and Date\_of\_duty\_allotted. 

Constraints: Primary key, license\_no must be unique, Bus 

capacity should not be null. 

CREATE TABLE Route ( 

Route\_no INT PRIMARY KEY, 

source VARCHAR(20), 

destination VARCHAR(20), 

no\_of\_stations INT 

); 

CREATE TABLE Bus ( 

Bus\_no INT PRIMARY KEY, 

capacity INT NOT NULL, 

depot\_name VARCHAR(20), 

Route\_no INT, 

FOREIGN KEY (Route\_no) REFERENCES Route(Route\_no) 

); 

CREATE TABLE Driver ( 

Driver\_no INT PRIMARY KEY, 

driver\_name VARCHAR(20), 

license\_no INT UNIQUE, 

address VARCHAR(20), 

age INT, 

salary FLOAT 

); 

CREATE TABLE Duty ( 

Bus\_no INT, 

Driver\_no INT, 

Shift INT, 

Date\_of\_duty\_allotted DATE, 

FOREIGN KEY (Bus\_no) REFERENCES Bus(Bus\_no), 

FOREIGN KEY (Driver\_no) REFERENCES Driver(Driver\_no), 

CHECK (Shift IN (1,2)) 

); 

INSERT INTO Route VALUES 

(101,'Pune','Mumbai',10), 

(102,'Pune','Nashik',8), 

(103,'Pune','Satara',6), 

(104,'Pune','Kolhapur',12), 

(105,'Pune','Solapur',9); 

INSERT INTO Bus VALUES 

(201,50,'Swargate',101), 

(202,45,'Pimpri',102), 

(203,40,'Shivajinagar',103), 

(204,55,'Hadapsar',104), 

(205,48,'Katraj',105); 

INSERT INTO Driver VALUES 

(301,'Ramesh',5001,'Pune',35,25000), 

(302,'Suresh',5002,'Pimpri',40,27000), 

(303,'Mahesh',5003,'Nigdi',38,26000), 

(304,'Ganesh',5004,'Chinchwad',36,25500), 

(305,'Rajesh',5005,'Akurdi',42,28000); 

INSERT INTO Duty VALUES 

(201,301,1,'2024-01-10'), 

(202,302,2,'2024-01-10'), 

(203,303,1,'2024-01-11'), 

(204,304,2,'2024-01-11'), 

(205,305,1,'2024-01-12'); 

SELECT \* FROM Route; 

SELECT \* FROM Bus; 

SELECT \* FROM Driver; 

SELECT \* FROM Duty; 

Using above database solve following Queries: 

Query 1. Display details of all the drivers having age more than 

40\. 

SELECT \* 

FROM Driver 

WHERE age > 40; 

Query 2. List driver name having salary > 30,000. 

SELECT driver\_name 

FROM Driver 

WHERE salary > 30000; 

Query 4. Display driver name who drive bus no ‘12’. 

SELECT D.driver\_name 

FROM Driver D, Duty DU 

WHERE D.Driver\_no = DU.Driver\_no 

AND DU.Bus\_no = 12; 

Q.2) Using above database solve following questions: 

1\. Write a trigger before inserting the driver record in driver 

table, if the age is between 18 and 

25, then display error message ‘Invalid input’. 

CREATE OR REPLACE FUNCTION check\_driver\_age() 

RETURNS TRIGGER 

LANGUAGE plpgsql 

AS $$ 

BEGIN 

IF NEW.age BETWEEN 18 AND 25 THEN 

RAISE EXCEPTION 'Invalid input'; 

END IF; 

RETURN NEW; 

END; 

$$; 

CREATE TRIGGER trg\_check\_driver\_age 

BEFORE INSERT 

ON Driver 

FOR EACH ROW 

EXECUTE FUNCTION check\_driver\_age(); 

Test the Trigger 

output 

postgres-# \\i s4t1.sql 

CREATE FUNCTION 

CREATE TRIGGER 

postgres=# INSERT INTO Driver VALUES 

(306,'Ajay',5006,'Pune',22,25000); 

ERROR:  Invalid input 

CONTEXT:  PL/pgSQL function check\_driver\_age() line 4 at RAISE 

2\. Write a stored function to display details of buses running 

on route\_no = ‘ ’. (Accept 

route\_no as an input parameter.) 

CREATE OR REPLACE FUNCTION bus\_route\_details(r\_no INT) 

RETURNS TABLE( 

bus\_no INT, 

capacity INT, 

depot\_name VARCHAR 

) 

LANGUAGE plpgsql 

AS $$ 

BEGIN 

RETURN QUERY 

SELECT B.bus\_no, B.capacity, B.depot\_name 

FROM Bus B 

WHERE B.route\_no = r\_no; 

END; 

$$; 

Call Function  

SELECT \* FROM bus\_route\_details(4); 





### SLIP 05 

Q1) Create the following database in 3NF using PostgresSQL. 

\[Total Marks: 10] 

Consider the following database of Bus-Transport System. Many 

buses run on one route. Drivers 

are allotted to buses shift-wise. 

Bus 

(Bus\_no int , capacity int , depot\_name varchar (20)) 

Route (Route\_no int, source varchar (20), destination varchar 

(20), no\_of\_stations int) 

Driver (Driver\_no int, driver\_name varchar (20), license\_no int, 

address varchar 

(20),age int , salary float) 

Relationship: 

Bus and Route related with many to one relationship. 

Bus and Driver related with many to many relationship with 

descriptive attributes, Shift – it can 

be 1 (Morning) or 2 (Evening) and Date\_of\_duty\_allotted. 

Constraints: Primary key, license\_no must be unique, Bus 

capacity should not be null. 

CREATE TABLE Route ( 

Route\_no INT PRIMARY KEY, 

source VARCHAR(20), 

destination VARCHAR(20), 

no\_of\_stations INT 

); 

CREATE TABLE Bus ( 

Bus\_no INT PRIMARY KEY, 

capacity INT NOT NULL, 

depot\_name VARCHAR(20), 

Route\_no INT, 

FOREIGN KEY (Route\_no) REFERENCES Route(Route\_no) 

); 

CREATE TABLE Driver ( 

Driver\_no INT PRIMARY KEY, 

driver\_name VARCHAR(20), 

license\_no INT UNIQUE, 

address VARCHAR(20), 

age INT, 

salary FLOAT 

); 

CREATE TABLE Duty ( 

Bus\_no INT, 

Driver\_no INT, 

Shift INT, 

Date\_of\_duty\_allotted DATE, 

PRIMARY KEY (Bus\_no, Driver\_no, Date\_of\_duty\_allotted), 

FOREIGN KEY (Bus\_no) REFERENCES Bus(Bus\_no), 

FOREIGN KEY (Driver\_no) REFERENCES Driver(Driver\_no), 

CHECK (Shift IN (1,2)) 

); 

INSERT INTO Route VALUES 

(101,'Pune','Mumbai',10), 

(102,'Pune','Nashik',8), 

(103,'Pune','Satara',6), 

(104,'Pune','Kolhapur',12), 

(105,'Pune','Solapur',9); 

INSERT INTO Bus VALUES 

(201,50,'Swargate',101), 

(202,45,'Pimpri',102), 

(203,40,'Shivajinagar',103), 

(204,55,'Hadapsar',104), 

(205,48,'Katraj',105); 

INSERT INTO Driver VALUES 

(301,'Ramesh',5001,'Pune',35,25000), 

(302,'Suresh',5002,'Pimpri',40,27000), 

(303,'Mahesh',5003,'Nigdi',38,26000), 

(304,'Ganesh',5004,'Chinchwad',36,25500), 

(305,'Rajesh',5005,'Akurdi',42,28000); 

INSERT INTO Duty VALUES 

(201,301,1,'2024-01-10'), 

(202,302,2,'2024-01-10'), 

(203,303,1,'2024-01-11'), 

(204,304,2,'2024-01-11'), 

(205,305,1,'2024-01-12'); 

SELECT \* FROM Route; 

SELECT \* FROM Bus; 

SELECT \* FROM Driver; 

SELECT \* FROM Duty; 

Using above database solve following Queries 

1\. Display the drivers name. 

SELECT driver\_name 

FROM Driver; 

2\. Display the route details on which buses of capacity 40 runs. 

SELECT R.\* 

FROM Route R, Bus B 

WHERE R.Route\_no = B.Route\_no 

AND B.capacity = 40; 

4\. Display bus detail of Rout\_no=4. 

SELECT \* 

FROM Bus 

WHERE Route\_no = 4; 

Q.2) Using above database solve following questions: 

1\. Create a View to Display driver details whose duty allotted 

on 26-9-2013. 

CREATE VIEW driver\_duty\_view AS 

SELECT D.\* 

FROM Driver D, Duty DU 

WHERE D.Driver\_no = DU.Driver\_no 

AND DU.Date\_of\_duty\_allotted = '2013-09-26'; 

To Display the View 

SELECT \* FROM driver\_duty\_view; 

2\. Write a function using cursor to display all the dates on 

which a driver has driven a bus 

(Accept the driver name as an input parameter) 

CREATE OR REPLACE FUNCTION driver\_duty\_dates(dname VARCHAR) 

RETURNS VOID 

LANGUAGE plpgsql 

AS $$ 

DECLARE 

duty\_date DATE; 

cur CURSOR FOR 

SELECT DU.Date\_of\_duty\_allotted 

FROM Driver D, Duty DU 

WHERE D.Driver\_no = DU.Driver\_no 

AND D.driver\_name = dname; 

BEGIN 

OPEN cur; 

LOOP 

FETCH cur INTO duty\_date; 

EXIT WHEN NOT FOUND; 

RAISE NOTICE 'Duty Date: %', duty\_date; 

END LOOP; 

CLOSE cur; 

END; 

$$; 

Output :  

postgres=# \\! vi c1.sql 

postgres=# \\i c1.sql 

CREATE FUNCTION 

postgres=# SELECT driver\_duty\_dates('Ramesh'); 

NOTICE:  Duty Date: 2024-01-10 

driver\_duty\_dates ------------------- 

(1 row) 





### SLIP 06  

Q.1) Create the following database in 3NF using PostgresSQL. 

Consider the following database of Student Competition. 

Student (Sid,sname,class) 

Competition (cno,cname ctype) 

\[Total Marks: 10] 

Relationship: 

Student and Competition related with many to many relationship 

with attribute rank and year. 

Constraints: Primary key sid,cno. 

Student Table 

CREATE TABLE Student ( 

Sid INTEGER PRIMARY KEY, 

sname VARCHAR(30), 

class VARCHAR(10) 

); 

Competition Table 

CREATE TABLE Competition ( 

cno INTEGER PRIMARY KEY, 

cname VARCHAR(30), 

ctype VARCHAR(20) 

); 

Participation Table (Relationship Table) 

CREATE TABLE Participation ( 

Sid INTEGER, 

cno INTEGER, 

rank INTEGER, 

year INTEGER, 

FOREIGN KEY (Sid) REFERENCES Student(Sid), 

FOREIGN KEY (cno) REFERENCES Competition(cno) 

); 

INSERT INTO Student VALUES 

(1,'Amit','SYBCA'), 

(2,'Anjali','SYBCA'), 

(3,'Rohit','TYBCA'), 

(4,'Sneha','FYBCA'), 

(5,'Akash','TYBCA'); 

INSERT INTO Competition VALUES 

(101,'Coding Contest','National'), 

(102,'Quiz Competition','State'), 

(103,'Hackathon','National'), 

(104,'Debugging Contest','College'), 

(105,'Project Exhibition','State'); 

INSERT INTO Participation VALUES 

(1,101,1,2023), 

(2,102,2,2023), 

(3,103,3,2022), 

(4,104,1,2023), 

(5,105,2,2022); 

SELECT \* FROM Student; 

SELECT \* FROM Competition; 

SELECT \* FROM Participation; 

Using above database solve following Queries: 

1\. Display students name. 

SELECT sname 

FROM Student; 

2\. Display name of student who took part in ‘football’. 

SELECT S.sname 

FROM Student S, Participation P, Competition C 

WHERE S.Sid = P.Sid 

AND C.cno = P.cno 

AND C.cname = 'Football'; 

3\. List the student detail who got 1 st rank. 

SELECT S.\* 

FROM Student S, Participation P 

WHERE S.Sid = P.Sid 

AND P.rank = 1; 

4\. List competition wise student count. 

SELECT C.cname, COUNT(P.Sid) AS student\_count 

FROM Competition C, Participation P 

WHERE C.cno = P.cno 

GROUP BY C.cname; 

Q.2) Using above database solve following questions: 

1\. Create a View to list name of student belongs to year 2018. 

CREATE VIEW student\_2018\_view AS 

SELECT S.sname 

FROM Student S, Participation P 

WHERE S.Sid = P.Sid 

AND P.year = 2018; 

To Display the View 

SELECT \* FROM student\_2018\_view; 

2\. Write a Stored Function to display details of competition of 

‘Pooja’. 

CREATE OR REPLACE FUNCTION competition\_details(name VARCHAR) 

RETURNS TABLE( 

cno INT, 

cname VARCHAR, 

ctype VARCHAR 

) 

LANGUAGE plpgsql 

AS $$ 

BEGIN 

RETURN QUERY 

SELECT C.cno, C.cname, C.ctype 

FROM Student S, Participation P, Competition C 

WHERE S.Sid = P.Sid 

AND C.cno = P.cno 

AND S.sname = name; 

END; 

$$; 

Output :  

postgres=# \\! vi c1.sql 

postgres=# \\! vi S1.sql 

postgres=# \\i S1.sql 

CREATE FUNCTION 

postgres=# SELECT \* FROM competition\_details('Pooja'); 

cno | cname | ctype -----+-------+------- 

(0 rows) 





### Slip 09  

Q.1) Create the following database in 3NF using PostgresSQL. 

Consider the following Project-Employee database, which is 

managed by a company and stores 

the details of projects assigned to employees. 

Project (Pno int, pname varchar (30), ptype varchar (20), 

duration integer) 

Employee (Eno integer, ename varchar (20), qualification char 

(15), joining\_date date) 

Relationship: 

Project-Employee related with many-to-many relationship, with 

descriptive attributes as 

start\_date\_of\_Project, no\_of\_hours\_worked. 

Constraints: Primary key, pname should not be null 

CREATE TABLE Project ( 

Pno INT PRIMARY KEY, 

pname VARCHAR(30) NOT NULL, 

ptype VARCHAR(20), 

duration INT 

); 

CREATE TABLE Employee ( 

Eno INT PRIMARY KEY, 

ename VARCHAR(20), 

qualification CHAR(15), 

joining\_date DATE 

); 

CREATE TABLE Project\_Employee ( 

Pno INT, 

Eno INT, 

start\_date\_of\_project DATE, 

no\_of\_hours\_worked INT, 

FOREIGN KEY (Pno) REFERENCES Project(Pno), 

FOREIGN KEY (Eno) REFERENCES Employee(Eno) 

); 

Using above database solve following Queries: 

1\. Display the project name, project type and project start 

date. 

SELECT P.pname, P.ptype, PE.start\_date\_of\_project 

FROM Project P, Project\_Employee PE 

WHERE P.Pno = PE.Pno; 

2\. Display details of employees working on ‘AI’ project. 

SELECT E.\* 

FROM Employee E, Project P, Project\_Employee PE 

WHERE E.Eno = PE.Eno 

AND P.Pno = PE.Pno 

AND P.pname = 'AI'; 

3\. List name of employee whose qualification is ‘M.Sc’. 

SELECT ename 

FROM Employee 

WHERE qualification = 'M.Sc'; 

4\. Count the number of employee. 

SELECT COUNT(\*) AS total\_employees 

FROM Employee; 

Q.2) Using above database solve following questions: 

1\. Write a trigger before inserting the duration into the 

project table and make sure that the 

duration is always greater than zero. Display appropriate 

message. 

CREATE OR REPLACE FUNCTION check\_duration() 

RETURNS TRIGGER 

LANGUAGE plpgsql 

AS $$ 

BEGIN 

IF NEW.duration <= 0 THEN 

RAISE EXCEPTION 'Duration must be greater than zero'; 

END IF; 

RETURN NEW; 

END; 

$$; 

CREATE TRIGGER trg\_check\_duration 

BEFORE INSERT 

ON Project 

FOR EACH ROW 

EXECUTE FUNCTION check\_duration(); 

Output  

slip09=# \\! vi s9.sql 

slip09=# \\! vi s9t.sql 

slip09=# \\i s9t.sql 

CREATE FUNCTION 

CREATE TRIGGER 

slip09=# INSERT INTO Project VALUES (6,'AI 

Project','Research',0); 

ERROR:  Duration must be greater than zero 

CONTEXT:  PL/pgSQL function check\_duration() line 4 at RAISE 

2\. Write function to accept project name as an input parameter 

and display names of 

employees working on that project. 

CREATE OR REPLACE FUNCTION emp\_project(p\_name VARCHAR) 

RETURNS TABLE(ename VARCHAR) 

LANGUAGE plpgsql 

AS $$ 

BEGIN 

RETURN QUERY 

SELECT E.ename 

FROM Employee E, Project P, Project\_Employee PE 

WHERE E.Eno = PE.Eno 

AND P.Pno = PE.Pno 

AND P.pname = p\_name; 

END; 

$$; 

Call Function 

SELECT \* FROM emp\_project('AI'); 





### Slip 10  

Q.1) Create the following database in 3NF using PostgresSQL. 

Consider the following Project-Employee database, which is 

managed by a company and stores 

the details of projects assigned to employees. 

Project (Pno int, pname varchar (30), ptype varchar (20), 

duration integer) 

Employee (Eno integer, ename varchar (20), qualification char 

(15), joining\_date date) 

Relationship: 

Project-Employee related with many-to-many relationship, with 

descriptive attributes as 

start\_date\_of\_Project, no\_of\_hours\_worked. 

Constraints: Primary key, pname should not be null 

CREATE TABLE Project ( 

Pno INT PRIMARY KEY, 

pname VARCHAR(30) NOT NULL, 

ptype VARCHAR(20), 

duration INT 

); 

CREATE TABLE Employee ( 

Eno INT PRIMARY KEY, 

ename VARCHAR(20), 

qualification CHAR(15), 

joining\_date DATE 

); 

CREATE TABLE Project\_Employee ( 

Pno INT, 

Eno INT, 

start\_date\_of\_project DATE, 

no\_of\_hours\_worked INT, 

FOREIGN KEY (Pno) REFERENCES Project(Pno), 

FOREIGN KEY (Eno) REFERENCES Employee(Eno) 

); 

Using above database solve following Queries: 

1\. List the name of employee whose name end by letter ‘L’. 

SELECT ename 

FROM Employee 

WHERE ename LIKE '%L'; 

2\. Insert 2 records into employee table. 

INSERT INTO Employee VALUES 

(106,'Sunil','M.Sc','2022-05-10'), 

(107,'Anil','B.Tech','2023-01-15'); 

3\. Display Project details which start on date’ 20-9-2017’. 

SELECT P.\* 

FROM Project P, Project\_Employee PE 

WHERE P.Pno = PE.Pno 

AND PE.start\_date\_of\_project = '2017-09-20'; 

4\. Display Project details whose duration>3. 

SELECT \* 

FROM Project 

WHERE duration > 3; 

Q.2) Using above database solve following questions: 

1\. Write a trigger before deleting an employee record from 

employee table. Raise a 

notice and display the message “Employee record is being 

deleted” 

CREATE OR REPLACE FUNCTION delete\_employee\_msg() 

RETURNS TRIGGER 

LANGUAGE plpgsql 

AS $$ 

BEGIN 

RAISE NOTICE 'Employee record is being deleted'; 

RETURN OLD; 

END; 

$$; 

CREATE TRIGGER trg\_delete\_employee 

BEFORE DELETE 

ON Employee 

FOR EACH ROW 

EXECUTE FUNCTION delete\_employee\_msg(); 

Output  

lip09=# \\! vi s99.sql 

slip09=# \\! vi s10.sql 

slip09=# \\! vi s11.sql 

slip09=# \\i s11.sql 

CREATE FUNCTION 

CREATE TRIGGER 

slip09=# DELETE FROM Employee 

WHERE Eno = 101; 

NOTICE:  Employee record is being deleted 

ERROR:  update or delete on table "employee" violates foreign 

key constraint "project\_employee\_eno\_fkey" on table 

"project\_employee" 

DETAIL:  Key (eno)=(101) is still referenced from table 

"project\_employee". 

slip09=# \\! vi s11.sql 

slip09=# 

2.Create a View To display employee details and it should be 

sorted by employee’s 

name in descending order 

CREATE VIEW emp\_desc\_view AS 

SELECT \* 

FROM Employee 

ORDER BY ename DESC; 

To Display the View 

SELECT \* FROM emp\_desc\_view; 





### Slip 11  

Q.1) Practical Questions on PostgresSQL 

\[Total Marks 10] 

Consider the following database 

Student (sname, rollno, phone) 

Course (cname, ccode, duration) 

A student can enroll in one or more courses. A course can have 

many students → Many-to-Many 

Relationship. 

CREATE TABLE Student( 

rollno INT PRIMARY KEY, 

sname VARCHAR(20), 

phone VARCHAR(15) 

); 

CREATE TABLE Course( 

ccode INT PRIMARY KEY, 

cname VARCHAR(30), 

duration INT 

); 

CREATE TABLE Enrollment( 

rollno INT, 

ccode INT, 

FOREIGN KEY(rollno) REFERENCES Student(rollno), 

FOREIGN KEY(ccode) REFERENCES Course(ccode) 

); 

INSERT INTO Student VALUES 

(1,'Amit','9876543210'), 

(2,'Sneha','9123456780'), 

(3,'Rahul','9988776655'), 

(4,'Pooja','9871234567'), 

(5,'Rohan','9012345678'); 

INSERT INTO Course VALUES 

(101,'DBMS',6), 

(102,'Java',4), 

(103,'Python',5), 

(104,'Web Development',6), 

(105,'Data Science',8); 

INSERT INTO Enrollment VALUES 

(1,101), 

(1,102), 

(2,103), 

(3,101), 

(4,104); 

Using above database, solve the following queries: 

1\. List details of courses with duration ‘6 months’ 

SELECT \* 

FROM Course 

WHERE duration = 6; 

2\. List student-wise course details. 

SELECT S.rollno, S.sname, C.cname, C.duration 

FROM Student S, Course C, Enrollment E 

WHERE S.rollno = E.rollno 

AND C.ccode = E.ccode; 

3\. Delete the student record of ‘Rohan’. 

DELETE FROM Student 

WHERE sname = 'Rohan'; 

4\. Update phone number of ‘Riya’ to 987654321. 

UPDATE Student 

SET phone = '987654321' 

WHERE sname = 'Riya'; 

Q.2) Using above database solve following questions: 

1\. Write a trigger before insert record of Student. If the 

rollno is less than or equal to zero and 

sname is null then give the appropriate message 

CREATE OR REPLACE FUNCTION check\_student() 

RETURNS TRIGGER 

LANGUAGE plpgsql 

AS $$ 

BEGIN 

IF NEW.rollno <= 0 AND NEW.sname IS NULL THEN 

RAISE EXCEPTION 'Invalid input: Roll number must be greater than 

zero and name cannot be NULL'; 

END IF; 

RETURN NEW; 

END; 

$$; 

CREATE TRIGGER trg\_student\_check 

BEFORE INSERT 

ON Student 

FOR EACH ROW 

EXECUTE FUNCTION check\_student(); 

TEST Trigger 

INSERT INTO Student VALUES (0,NULL,'9876543210'); 

2\. Write a stored function to accept course code as an input 

parameter and display course 

name with duration. 

CREATE OR REPLACE FUNCTION course\_details(c\_code INT) 

RETURNS TABLE(cname VARCHAR, duration INT) 

LANGUAGE plpgsql 

AS $$ 

BEGIN 

RETURN QUERY 

SELECT C.cname, C.duration 

FROM Course C 

WHERE C.ccode = c\_code; 

END; 

$$; 

lip09=# \\! vi s1011.sql 

slip09=# \\i s1011.sql 

CREATE FUNCTION 

slip09=# SELECT \* FROM course\_details(101); 

cname | duration -------+---------- 

DBMS  |     

6 

(1 row) 





### SLIP 12 

Q.1) Practical Questions on PostgresSQL 

Consider the following database 

Employee (emp\_id, emp\_name, address, bdate) 

Investor (inv\_no, inv\_name, inv\_date,inv\_amt) 

An employee may invest in one or more investments; hence he can 

be an investor. But an 

investor need not be an employee of the firm. 

Assume appropriate data types for all the attributes. 

CREATE TABLE Employee( 

emp\_id INT PRIMARY KEY, 

emp\_name VARCHAR(30), 

address VARCHAR(40), 

bdate DATE 

); 

CREATE TABLE Investor( 

inv\_no INT PRIMARY KEY, 

inv\_name VARCHAR(30), 

inv\_date DATE, 

inv\_amt DECIMAL(10,2) 

); 

CREATE TABLE Investment( 

emp\_id INT, 

inv\_no INT, 

FOREIGN KEY(emp\_id) REFERENCES Employee(emp\_id), 

FOREIGN KEY(inv\_no) REFERENCES Investor(inv\_no) 

); 

Using above database, solve the following queries: 

1\. List the names of employees 

SELECT emp\_name 

FROM Employee; 

2\. List the information of Investors whose name start with ‘M’ 

SELECT \* 

FROM Investor 

WHERE inv\_name LIKE 'M%'; 

3\. Increase the amount of investors by 10%. 

UPDATE Investor 

SET inv\_amt = inv\_amt \* 1.10; 

4\. Delete the investor information whose amount is 20,000. 

DELETE FROM Investor 

WHERE inv\_amt = 20000; 

Q.2) Using above database solve following questions: 

1\. Write a trigger to validate the investment amount approved. 

It must be less than or 

equal to 2000/-. Display appropriate message. 

CREATE OR REPLACE FUNCTION check\_inv\_amt() 

RETURNS TRIGGER 

LANGUAGE plpgsql 

AS $$ 

BEGIN 

IF NEW.inv\_amt > 2000 THEN 

RAISE EXCEPTION 'Investment amount must be less than or equal to 

2000'; 

END IF; 

RETURN NEW; 

END; 

$$; 

CREATE TRIGGER trg\_inv\_amt 

BEFORE INSERT OR UPDATE 

ON Investor 

FOR EACH ROW 

EXECUTE FUNCTION check\_inv\_amt(); 

Test the Trigger 

INSERT INTO Investor VALUES (106,'Mahesh','2023-06-10',3000); 

Output :  

slip12=# \\! vi s12t.sql 

slip12=# \\i s12t.sql 

CREATE FUNCTION 

CREATE TRIGGER 

slip12=# INSERT INTO Investor VALUES 

(106,'Mahesh','2023-06-10',3000); 

ERROR:  Investment amount must be less than or equal to 2000 

CONTEXT:  PL/pgSQL function check\_inv\_amt() line 4 at RAISE 

2\. Write a stored function to count number of investor. (Accept 

investor name as an input 

parameter). Display message for invalid investor name. 

CREATE OR REPLACE FUNCTION count\_investor(i\_name VARCHAR) 

RETURNS INT 

LANGUAGE plpgsql 

AS $$ 

DECLARE 

total INT; 

BEGIN 

SELECT COUNT(\*) INTO total 

FROM Investor 

WHERE inv\_name = i\_name; 

IF total = 0 THEN 

RAISE NOTICE 'Invalid investor name'; 

END IF; 

RETURN total; 

END; 

$$; 

SELECT count\_investor('Amit'); 





### Slip 13 

Q.1) Practical Questions on PostgresSQL 

Consider the following database 

Employee: (ename, empid, phone)  // Also take bdate it required 

in creating view. 

Department: (dname, deptid, location) 

Relationship: An employee belongs to exactly one department; a 

department has many employees. 

slip13=# CREATE TABLE Department ( 

deptid INT PRIMARY KEY, 

dname VARCHAR(50), 

location VARCHAR(50) 

); 

CREATE TABLE 

slip13=# CREATE TABLE Employee ( 

empid INT PRIMARY KEY, 

ename VARCHAR(50), 

phone VARCHAR(15), 

bdate DATE, 

deptid INT, 

FOREIGN KEY (deptid) REFERENCES Department(deptid) 

); 

CREATE TABLE 

INSERT INTO Department VALUES 

(1,'Computer science','Pune'), 

(2,'IT','Mumbai'), 

(3,'Mechanical','Nashik'), 

(4,'Civil','Satara'), 

(5,'Electrical','Kolhapur'); 

INSERT INTO Employee VALUES 

(101,'Amit','9876543210',1), 

(102,'Sneha','9123456780',2), 

(103,'Rahul','9988776655',1), 

(104,'Pooja','9871234567',3), 

(105,'Rohan','9012345678',4); 

Using above database, solve the following queries: 

1\. List employees in ‘Computer science’ department 

SELECT E.\* 

FROM Employee E, Department D 

WHERE E.deptid = D.deptid 

AND D.dname = 'Computer science'; 

2\. Delete the employee whose names starts with ‘S’ 

DELETE FROM Employee 

WHERE ename LIKE 'S%'; 

3\. Insert 2 records into Deapartment. 

INSERT INTO Department VALUES 

(6,'Electronics','Pune'), 

(7,'Computer Engineering','Mumbai'); 

4\. Display employee name along with department. 

SELECT E.ename, D.dname 

FROM Employee E, Department D 

WHERE E.deptid = D.deptid; 

Q.2) Using above database solve following questions: 

\[Total Marks: 20] 

1\. Create a View: 

1\. To display Employee details that have birth date before 1900 

year. 

2\. To display employee name with phone number. 

CREATE VIEW emp\_before\_1900 AS 

SELECT \* 

FROM Employee 

WHERE bdate < '1900-01-01'; 

CREATE VIEW emp\_name\_phone AS 

SELECT ename, phone 

FROM Employee; 

To Display Views 

SELECT \* FROM emp\_before\_1900; 

SELECT \* FROM emp\_name\_phone; 

2\. Write a cursor to display employee details along with their 

department name. 

CREATE OR REPLACE FUNCTION emp\_dept\_cursor() 

RETURNS VOID 

LANGUAGE plpgsql 

AS $$ 

DECLARE 

rec RECORD; 

cur CURSOR FOR 

SELECT E.empid, E.ename, E.phone, D.dname 

FROM Employee E, Department D 

WHERE E.deptid = D.deptid; 

BEGIN 

OPEN cur; 

LOOP 

FETCH cur INTO rec; 

EXIT WHEN NOT FOUND; 

RAISE NOTICE 'ID: %, Name: %, Phone: %, Dept: %', 

rec.empid, rec.ename, rec.phone, rec.dname; 

END LOOP; 

CLOSE cur; 

END; 

$$; 

Call function 

SELECT emp\_dept\_cursor(); 

Output  

slip13=# \\i s13c.sql 

CREATE FUNCTION 

slip13=# SELECT emp\_dept\_cursor(); 

NOTICE:  ID: 101, Name: Amit, Phone: 9876543210, Dept: Computer 

science 

NOTICE:  ID: 102, Name: Sneha, Phone: 9123456780, Dept: IT 

NOTICE:  ID: 103, Name: Rahul, Phone: 9988776655, Dept: Computer 

science 

NOTICE:  ID: 104, Name: Pooja, Phone: 9871234567, Dept: 

Mechanical 

NOTICE:  ID: 105, Name: Rohan, Phone: 9012345678, Dept: Civil 

emp\_dept\_cursor ----------------- 

(1 row) 





### Slip 15. 

Consider the following database 

Book: (btitle, ISBN,category) 

Author: (aname, address, phone) 

Relationship: A book has exactly one author; an author can write 

many books. 

slip15=# CREATE TABLE Author ( 

aname VARCHAR(30) PRIMARY KEY, 

address VARCHAR(50), 

phone VARCHAR(15) 

); 

CREATE TABLE 

slip15=# CREATE TABLE Book ( 

ISBN INT PRIMARY KEY, 

btitle VARCHAR(50), 

category VARCHAR(30), 

aname VARCHAR(30), 

FOREIGN KEY (aname) REFERENCES Author(aname) 

); 

CREATE TABLE 

INSERT INTO Author VALUES 

('Chetan Bhagat','Delhi','9876543210'), 

('J.K. Rowling','UK','9123456780'), 

('Dan Brown','USA','9988776655'), 

('R.K. Narayan','India','9871234567'), 

('Paulo Coelho','Brazil','9012345678'); 

INSERT INTO Book VALUES 

(101,'Five Point Someone','Fiction','Chetan Bhagat'), 

(102,'Harry Potter','Fantasy','J.K. Rowling'), 

(103,'The Da Vinci Code','Thriller','Dan Brown'), 

(104,'Malgudi Days','Classic','R.K. Narayan'), 

(105,'The Alchemist','Philosophy','Paulo Coelho'); 

Using above database, solve the following queries: 

1\. Insert 2 records into book table. 

2\. List author-wise book details. 

3\. List books written by ‘J.K. Rowling’. 

4\. Update phone number of ‘George Martin’ to 987654321 

Solution 

1⃣ Insert 2 records into Book table 

INSERT INTO Book VALUES 

(106,'2 States','Fiction','Chetan Bhagat'), 

(107,'Inferno','Thriller','Dan Brown'); 

2⃣ List author-wise book details 

SELECT A.aname, B.btitle, B.category 

FROM Author A, Book B 

WHERE A.aname = B.aname; 

✔ Shows each author with their books 

3⃣ List books written by ‘J.K. Rowling’ 

SELECT btitle 

FROM Book 

WHERE aname = 'J.K. Rowling'; 

✔ Displays all books of J.K. Rowling 

4⃣ Update phone number of ‘George Martin’ 

UPDATE Author 

SET phone = '987654321' 

WHERE aname = 'George Martin'; 

Q2) Using above database solve following questions: 

\[Total Marks: 20] 

1\. Write a trigger before inserting the Book record in Book 

table, if Author name is not 

exist then print ”Invalid Name” 

CREATE OR REPLACE FUNCTION check\_author\_exist() 

RETURNS TRIGGER 

LANGUAGE plpgsql 

AS $$ 

BEGIN 

IF NOT EXISTS ( 

SELECT 1 FROM Author A 

WHERE A.aname = NEW.aname 

) THEN 

RAISE EXCEPTION 'Invalid Name'; 

END IF; 

RETURN NEW; 

END; 

$$; 

CREATE TRIGGER trg\_check\_author 

BEFORE INSERT 

ON Book 

FOR EACH ROW 

EXECUTE FUNCTION check\_author\_exist(); 

Output 

slip13=# \\! vi s15t.sql 

slip13=# \\i s15t.sql 

CREATE FUNCTION 

CREATE TRIGGER 

slip13=# INSERT INTO Book VALUES (108,'Unknown 

Book','Fiction','XYZ'); 

ERROR:  Invalid Name 

CONTEXT:  PL/pgSQL function check\_author\_exist() line 7 at RAISE 

2\. Write a function using cursor to display all Book of Specific 

Author. 

CREATE OR REPLACE FUNCTION books\_by\_author(a\_name VARCHAR) 

RETURNS VOID 

LANGUAGE plpgsql 

AS $$ 

DECLARE 

rec RECORD; 

cur CURSOR FOR 

SELECT btitle, category 

FROM Book 

WHERE aname = a\_name; 

BEGIN 

OPEN cur; 

LOOP 

FETCH cur INTO rec; 

EXIT WHEN NOT FOUND; 

RAISE NOTICE 'Book: %, Category: %', 

rec.btitle, rec.category; 

END LOOP; 

CLOSE cur; 

END; 

$$; 

Output 

slip15=# \\i s15c.sql 

CREATE FUNCTION 

slip13=# SELECT books\_by\_author('J.K. Rowling'); 

NOTICE:  Book: Harry Potter, Category: Fantasy 

books\_by\_author ----------------- 

(1 row) 

&#x20;

&#x20;                                

&#x20;

### Slip 17 

Q1) Practical Questions on PostgresSQL 

\[Total Marks 10] 

Consider the following database 

Patient: ( pid,pname, age) 

Doctor:(did,dname, specialization) 

Relationship: A doctor can have many patients; each patient is 

assigned to exactly one doctor. 

CREATE TABLE Doctor ( 

did INT PRIMARY KEY, 

dname VARCHAR(30), 

specialization VARCHAR(30) 

); 

CREATE TABLE Patient ( 

pid INT PRIMARY KEY, 

pname VARCHAR(30), 

age INT, 

did INT, 

FOREIGN KEY (did) REFERENCES Doctor(did) 

); 

INSERT INTO Doctor VALUES 

(1,'Dr. Sharma','Cardiologist'), 

(2,'Dr. Mehta','Neurologist'), 

(3,'Dr. Patil','Orthopedic'), 

(4,'Dr. Desai','Dermatologist'), 

(5,'Dr. Joshi','Pediatrician'); 

INSERT INTO Patient VALUES 

(101,'Amit',25,1), 

(102,'Sneha',30,2), 

(103,'Rahul',28,3), 

(104,'Pooja',22,1), 

(105,'Rohan',35,5); 

Using above database, solve the following queries: 

1\. List the patients name of doctor ‘Dr. Sharma’ . 

2\. List doctors all details. 

3\. Update specialization of ‘Dr. Gupta’ to ‘Cardiologist’. 

4\. Delete all patients of ‘Dr.Agarwal’. 

1⃣ List the patient names of doctor ‘Dr. Sharma’ 

SELECT P.pname 

FROM Patient P, Doctor D 

WHERE P.did = D.did 

AND D.dname = 'Dr. Sharma'; 

2⃣ List all doctor details 

SELECT \* 

FROM Doctor; 

3⃣ Update specialization of ‘Dr. Gupta’ to ‘Cardiologist’ 

UPDATE Doctor 

SET specialization = 'Cardiologist' 

WHERE dname = 'Dr. Gupta'; 

⚠ If ‘Dr. Gupta’ does not exist → 0 rows updated 

4⃣ Delete all patients of ‘Dr. Agarwal’ 

DELETE FROM Patient 

WHERE did = ( 

SELECT did 

FROM Doctor 

WHERE dname = 'Dr. Agarwal' 

); 

Q.2) Using above database solve following questions: 

1\. Create a View: 

a. To display Doctor details who check patient ”Mr.P.V.Joshi”. 

b. To display Patient name with age. 

1⃣ View: Doctor details who check patient “Mr.P.V.Joshi” 

CREATE VIEW doctor\_of\_joshi AS 

SELECT D.\* 

FROM Doctor D, Patient P 

WHERE D.did = P.did 

AND P.pname = 'Mr.P.V.Joshi'; 

2⃣ View: Patient name with age 

CREATE VIEW patient\_name\_age AS 

SELECT pname, age 

FROM Patient; 

To Display Views 

SELECT \* FROM doctor\_of\_joshi; 

SELECT \* FROM patient\_name\_age; 

2\. Write a cursor to display doctor details along with patient. 

CREATE OR REPLACE FUNCTION doctor\_patient\_cursor() 

RETURNS VOID 

LANGUAGE plpgsql 

AS $$ 

DECLARE 

rec RECORD; 

cur CURSOR FOR 

SELECT D.did, D.dname, D.specialization, 

P.pid, P.pname, P.age 

FROM Doctor D, Patient P 

WHERE D.did = P.did; 

BEGIN 

OPEN cur; 

LOOP 

FETCH cur INTO rec; 

EXIT WHEN NOT FOUND; 

RAISE NOTICE 'Doctor ID: %, Name: %, Spec: %, Patient ID: 

%, Patient: %, Age: %', 

rec.did, rec.dname, rec.specialization, 

rec.pid, rec.pname, rec.age; 

END LOOP; 

CLOSE cur; 

END; 

$$; 

Output  

postgres=# \\! vi s17.sql 

postgres=# \\i s17.sql 

CREATE FUNCTION 

postgres=# SELECT doctor\_patient\_cursor(); 

NOTICE:  Doctor ID: 1, Name: Dr. Sharma, Spec: Cardiologist, 

Patient ID: 101, Patient: Amit, Age: 25 

NOTICE:  Doctor ID: 2, Name: Dr. Mehta, Spec: Neurologist, 

Patient ID: 102, Patient: Sneha, Age: 30 

NOTICE:  Doctor ID: 3, Name: Dr. Patil, Spec: Orthopedic, 

Patient ID: 103, Patient: Rahul, Age: 28 

NOTICE:  Doctor ID: 1, Name: Dr. Sharma, Spec: Cardiologist, 

Patient ID: 104, Patient: Pooja, Age: 22 

NOTICE:  Doctor ID: 5, Name: Dr. Joshi, Spec: Pediatrician, 

Patient ID: 105, Patient: Rohan, Age: 35 

doctor\_patient\_cursor ----------------------- 

(1 row) 





### Slip 18 

Q1) Practical Questions on PostgresSQL 

\[Total Marks 10] 

Consider the following database 

Product: (pname, pid, price) 

Order: (oid, orderdate, customer) 

Relationship: A product can be in many orders; an order can 

contain many products. 

postgres=# CREATE TABLE Product ( 

pid INT PRIMARY KEY, 

pname VARCHAR(30), 

price DECIMAL(10,2) 

); 

CREATE TABLE 

postgres=# CREATE TABLE Orders ( 

oid INT PRIMARY KEY, 

orderdate DATE, 

customer VARCHAR(30) 

); 

CREATE TABLE 

postgres=# CREATE TABLE Order\_Product ( 

pid INT, 

oid INT, 

FOREIGN KEY (pid) REFERENCES Product(pid), 

FOREIGN KEY (oid) REFERENCES Orders(oid) 

); 

CREATE TABLE 

postgres=# INSERT INTO Product VALUES 

(1,'Laptop',60000), 

(2,'Mobile',20000), 

(3,'Tablet',15000), 

(4,'Printer',10000), 

(5,'Keyboard',2000); 

INSERT 0 5 

postgres=# INSERT INTO Orders VALUES 

(101,'2023-01-10','Amit'), 

(102,'2023-02-15','Sneha'), 

(103,'2023-03-20','Rahul'), 

(104,'2023-04-25','Pooja'), 

(105,'2023-05-30','Rohan'); 

INSERT 0 5 

postgres=# INSERT INTO Order\_Product VALUES 

(1,101), 

(2,101), 

(3,102), 

(4,103), 

(5,104); 

INSERT 0 5 

postgres=# 

Using above database, solve the following queries: 

1\. List products of order id 101. 

2\. List Product details. 

3\. Display product name and price. 

4\. Insert 2 records into product table. 

Solution 

1⃣ List products of order id 101 

SELECT P.\* 

FROM Product P, Order\_Product OP 

WHERE P.pid = OP.pid 

AND OP.oid = 101; 

2⃣ List Product details 

SELECT \* 

FROM Product; 

3⃣ Display product name and price 

SELECT pname, price 

FROM Product; 

4⃣ Insert 2 records into Product table 

INSERT INTO Product VALUES 

(6,'Mouse',800), 

(7,'Monitor',12000); 

Q.2) Using above database solve following questions: 

\[Total Marks: 20] 

1\. Create a View: 

a) To display product details. 

b) To display the order with the highest demand . 

a) CREATE VIEW product\_details AS 

SELECT \* 

FROM Product; 

b) SELECT oid, COUNT(pid) 

FROM Order\_Product 

GROUP BY oid 

ORDER BY COUNT(pid) DESC; 

To Display Views 

SELECT \* FROM product\_details; 

SELECT \* FROM highest\_demand\_order; 

2\. Write a function to display all details of product with 

customer . 

CREATE OR REPLACE FUNCTION product\_customer\_details() 

RETURNS TABLE ( 

pid INT, 

pname VARCHAR, 

price DECIMAL, 

oid INT, 

customer VARCHAR 

) 

LANGUAGE plpgsql 

AS $$ 

BEGIN 

RETURN QUERY 

SELECT P.pid, P.pname, P.price, 

O.oid, O.customer 

FROM Product P, Orders O, Order\_Product OP 

WHERE P.pid = OP.pid 

AND O.oid = OP.oid; 

END; 

$$; 

Output 

postgres=# \\! vi s18.sql 

postgres=# \\i s18.sql 

CREATE FUNCTION 

postgres=# SELECT \* FROM product\_customer\_details(); 

pid |  pname   |  price   | oid | customer -----+----------+----------+-----+---------- 

1 | Laptop   | 60000.00 | 101 | Amit 

2 | Mobile   | 20000.00 | 101 | Amit 

3 | Tablet   | 15000.00 | 102 | Sneha 

4 | Printer  | 10000.00 | 103 | Rahul 

5 | Keyboard |  2000.00 | 104 | Pooja 

(5 rows) 





### Slip 19 

Q1) Practical Questions on PostgresSQL 

\[ Total Marks 10] 

Consider the following database 

Student (sreg\_no, sname , class) 

Competition (c\_no , cname , C\_type) 

The relationship is as follows: 

STUDENT-COMPETITION: M-M with described attributes rank and 

year. 

Class should be ‘FYBCA’ , ‘SYBCA’ and ‘TYBCA’ 

Assume appropriate data types for all the attributes. 

slip19=# CREATE TABLE Student ( 

sreg\_no INT PRIMARY KEY, 

sname VARCHAR(30), 

class VARCHAR(10) CHECK (class IN 

('FYBCA','SYBCA','TYBCA')) 

); 

CREATE TABLE 

slip19=# CREATE TABLE Competition ( 

c\_no INT PRIMARY KEY, 

cname VARCHAR(30), 

c\_type VARCHAR(20) 

); 

CREATE TABLE 

slip19=# CREATE TABLE Student\_Competition ( 

sreg\_no INT, 

c\_no INT, 

rank INT, 

year INT, 

FOREIGN KEY (sreg\_no) REFERENCES Student(sreg\_no), 

FOREIGN KEY (c\_no) REFERENCES Competition(c\_no) 

); 

CREATE TABLE 

slip19=# INSERT INTO Student VALUES 

(1,'Pooja','FYBCA'), 

(2,'Rahul','SYBCA'), 

(3,'Sneha','TYBCA'), 

(4,'Amit','FYBCA'), 

(5,'Riya','SYBCA'); 

INSERT 0 5 

slip19=# INSERT INTO Competition VALUES 

(101,'Coding Contest','Technical'), 

(102,'Quiz','Academic'), 

(103,'Hackathon','Technical'), 

(104,'Debate','Non-Technical'), 

(105,'Poster Making','Creative'); 

INSERT 0 5 

slip19=# INSERT INTO Student\_Competition VALUES 

(1,101,1,2023), 

(2,102,2,2023), 

(3,103,1,2022), 

(4,104,3,2023), 

(5,105,2,2022); 

INSERT 0 5 

slip19=# 

1⃣ List names of students who participated in Quiz Competition 

SELECT S.sname 

FROM Student S, Competition C, Student\_Competition SC 

WHERE S.sreg\_no = SC.sreg\_no 

AND C.c\_no = SC.c\_no 

AND C.cname = 'Quiz'; 

2⃣ List names of competitions 

SELECT cname 

FROM Competition; 

3⃣ Display details of students whose name starts with ‘A’ 

SELECT \* 

FROM Student 

WHERE sname LIKE 'A%'; 

4⃣ Insert 2 records into Student table 

INSERT INTO Student VALUES 

(6,'Anjali','FYBCA'), 

(7,'Arjun','TYBCA'); 

Q.2) Using above database solve following questions: 

\[Total Marks: 20] 

1\. Write a trigger after insert on student table to display 

message “All students are 

welcome for competition” if student participate more than 2 

competition. 

CREATE OR REPLACE FUNCTION check\_participation() 

RETURNS TRIGGER 

LANGUAGE plpgsql 

AS $$ 

DECLARE 

comp\_count INT; 

BEGIN -- Count competitions for inserted student 

SELECT COUNT(\*) INTO comp\_count 

FROM Student\_Competition 

WHERE sreg\_no = NEW.sreg\_no; -- Check condition 

IF comp\_count > 2 THEN 

RAISE NOTICE 'All students are welcome for competition'; 

END IF; 

RETURN NEW; 

END; 

$$; 

CREATE TRIGGER trg\_after\_insert\_student 

AFTER INSERT ON Student 

FOR EACH ROW 

EXECUTE FUNCTION check\_participation(); 

&#x20;

&#x20;

Practically, participation data is in Student\_Competition, 

so: 

● This trigger may not show message immediately after insert 

● But it is written as per question requirement 

✅

&#x20;

Correct logical trigger (on Student\_Competition  

table) 

CREATE OR REPLACE FUNCTION check\_participation\_sc() 

RETURNS TRIGGER 

LANGUAGE plpgsql 

AS $$ 

DECLARE 

comp\_count INT; 

BEGIN -- Count competitions for the student 

SELECT COUNT(\*) INTO comp\_count 

FROM Student\_Competition 

WHERE sreg\_no = NEW.sreg\_no; -- Condition 

IF comp\_count > 2 THEN 

RAISE NOTICE 'All students are welcome for competition'; 

END IF; 

RETURN NEW; 

END; 

$$; 

CREATE TRIGGER trg\_after\_insert\_sc 

AFTER INSERT ON Student\_Competition 

FOR EACH ROW 

EXECUTE FUNCTION check\_participation\_sc(); 

If you insert: 

INSERT INTO Student\_Competition VALUES (1,102,2,2024); 

INSERT INTO Student\_Competition VALUES (1,103,1,2024); 

INSERT INTO Student\_Competition VALUES (1,104,3,2024); 

&#x20;

&#x20;

On 3rd insert → message appears: 

NOTICE: All students are welcome for competition 





### Slip 20 

Q.1) Practical Questions on PostgresSQL 

Consider the following database 

Property (pno, description, area) 

Owner (oname, address, phone) 

An owner can have one or more properties, but a property belongs 

to exactly one owner. Create 

the relations accordingly, so that the relationship is handled 

properly and the relations are 

handled properly and the relations are in normalized form (3NF). 

CREATE TABLE Owner ( 

owner\_id SERIAL PRIMARY KEY, 

oname VARCHAR(30), 

address VARCHAR(50), 

phone VARCHAR(15) 

); 

CREATE TABLE Property ( 

pno INT PRIMARY KEY, 

description VARCHAR(50), 

area VARCHAR(30), 

owner\_id INT, 

FOREIGN KEY (owner\_id) REFERENCES Owner(owner\_id) 

); 

Using above database, solve the following queries: 

1\. List details of property where area is ‘Moshi’ 

2\. List the owners detail. 

3\. List property details owned by ‘Mr. Kadam’ 

4\. Update phone no of ‘Mr. Patil’ to 987842621 

1⃣ List details of property where area is ‘Moshi’ 

SELECT \* 

FROM Property 

WHERE area = 'Moshi'; 

2⃣ List the owners detail 

SELECT \* 

FROM Owner; 

3⃣ List property details owned by ‘Mr. Kadam’ 

SELECT P.\* 

FROM Property P, Owner O 

WHERE P.owner\_id = O.owner\_id 

AND O.oname = 'Mr. Kadam'; 

4⃣ Update phone no of ‘Mr. Patil’ to 987842621 

UPDATE Owner 

SET phone = '987842621' 

WHERE oname = 'Mr. Patil'; 

Q2) Using above database solve following questions: 

1\. Write a trigger before inserting description into a Property 

table; that area should in Pune. Display appropriate message. 

CREATE OR REPLACE FUNCTION check\_area\_pune() 

RETURNS TRIGGER 

LANGUAGE plpgsql 

AS $$ 

BEGIN -- Check if area is Pune 

IF NEW.area <> 'Pune' THEN 

RAISE EXCEPTION 'Area must be Pune'; 

ELSE 

RAISE NOTICE 'Property inserted in Pune area'; 

END IF; 

RETURN NEW; 

END; 

$$; 

CREATE TRIGGER trg\_before\_insert\_property 

BEFORE INSERT ON Property 

FOR EACH ROW 

EXECUTE FUNCTION check\_area\_pune(); 

Output 

INSERT INTO Property VALUES (101,'Flat','Pune',1);   -- 

✅

&#x20;

Allowed 

INSERT INTO Property VALUES (102,'Shop','Mumbai',1); -- 

❌

&#x20;Error 

2\. Write a cursor to list the details of all Owners. 

DO $$ 

DECLARE 

rec RECORD; 

owner\_cursor CURSOR FOR 

SELECT \* FROM Owner; 

BEGIN 

OPEN owner\_cursor; 

LOOP 

FETCH owner\_cursor INTO rec; 

EXIT WHEN NOT FOUND; 

RAISE NOTICE 'Name: %, Address: %, Phone: %', 

rec.oname, rec.address, rec.phone; 

END LOOP; 

CLOSE owner\_cursor; 

END; 

$$; 

Output  

NOTICE: Name: Mr. Patil, Address: Pune, Phone: 987842621 

NOTICE: Name: Mr. Kadam, Address: Mumbai, Phone: 987654321 

