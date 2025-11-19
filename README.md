# Library Management System using SQL Project --P2

## Project Overview

**Project Title**: Library Management System  

This project demonstrates the implementation of a Library Management System using SQL. It includes creating and managing tables, performing CRUD operations. The goal is to showcase skills in database design, manipulation, and querying.

## Objectives

1. **Set up the Library Management System Database**: Create and populate the database with tables for branches, employees, members, books, issued status, and return status.
2. **CRUD Operations**: Perform Create, Read, Update, and Delete operations on the data.
3. **CTAS (Create Table As Select)**: Utilize CTAS to create new tables based on query results.

## Project Structure

### 1. Database Setup
![ERD](https://github.com/GT-Creat0r/Library-Management-System-SQL-Project-P2/blob/main/LibraryERD.png)

- **Database Creation**: Created a database named `sql_project_p2`.
- **Table Creation**: Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.

```sql
--Creating Branch table
DROP TABLE IF EXISTS branch;
 CREATE TABLE branch(
	branch_id VARCHAR(10) PRIMARY KEY,
	manager_id	VARCHAR(10),
	branch_address	VARCHAR(55),
	contact_no VARCHAR(10)
 );
ALTER TABLE branch
ALTER COLUMN contact_no TYPE VARCHAR(20);

--Creating Employees table
DROP TABLE IF EXISTS employees;
CREATE TABLE employees(
	emp_id VARCHAR(10) PRIMARY KEY,
	emp_name VARCHAR(25),
	position VARCHAR(15),
	salary INT,
	branch_id VARCHAR(10) --FK
);

--Creating books table
DROP TABLE IF EXISTS books;
CREATE TABLE books(
	isbn VARCHAR(20) PRIMARY KEY,
	book_title VARCHAR(75),
	category VARCHAR(25),
	rental_price FLOAT,
	status VARCHAR(10),
	author VARCHAR(35),
	publisher VARCHAR(55)
);

--Creating members table
DROP TABLE IF EXISTS members;
CREATE TABLE members(
	member_id VARCHAR(15) PRIMARY KEY,
	member_name VARCHAR(30),
	member_address VARCHAR(30),
	reg_date DATE
);

--Creating issued_status table
DROP TABLE IF EXISTS issued_status;
CREATE TABLE issued_status (
	issued_id VARCHAR(10) PRIMARY KEY,
	issued_member_id VARCHAR(10), --FK
	issued_book_name VARCHAR(75),
	issued_date	DATE,
	issued_book_isbn VARCHAR(20), --FK
	issued_emp_id VARCHAR(10) --FK
);

--Creating return_status table
DROP TABLE IF EXISTS return_status;
CREATE TABLE return_status (	
	return_id	VARCHAR(10) PRIMARY KEY,
	issued_id	VARCHAR(10),  --FK
	return_book_name VARCHAR(75),
	return_date DATE,
	return_book_isbn VARCHAR(20)
)

--Foreign key
ALTER TABLE issued_status
ADD CONSTRAINT fk_members
FOREIGN KEY (issued_member_id)
REFERENCES members(member_id);

ALTER TABLE issued_status
ADD CONSTRAINT fk_books
FOREIGN KEY (issued_book_isbn)
REFERENCES books(isbn);

ALTER TABLE issued_status
ADD CONSTRAINT fk_employees
FOREIGN KEY (issued_emp_id)
REFERENCES employees(emp_id);

ALTER TABLE employees 
ADD CONSTRAINT fk_branch
FOREIGN KEY (branch_id)
REFERENCES branch(branch_id);

ALTER TABLE return_status
ADD CONSTRAINT fk_issued_status
FOREIGN KEY (issued_id)
REFERENCES issued_status(issued_id);


```

### 2. CRUD Operations

- **Create**: Inserted sample records into the `books` table.
- **Read**: Retrieved and displayed data from various tables.
- **Update**: Updated records in the `employees` table.
- **Delete**: Removed records from the `members` table as needed.

**Task 1. Create a New Book Record**
-- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"

```sql
INSERT INTO books(isbn, book_title, category, rental_price, status, author, publisher)
VALUES('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
SELECT * FROM books;
```
**Task 2: Update an Existing Member's Address**

```sql
UPDATE members
SET member_address = '125 Oak St'
WHERE member_id = 'C103';
```

**Task 3: Delete a Record from the Issued Status Table**
-- Objective: Delete the record with issued_id = 'IS121' from the issued_status table.

```sql
DELETE FROM issued_status
WHERE   issued_id =   'IS121';
```

**Task 4: Retrieve All Books Issued by a Specific Employee**
-- Objective: Select all books issued by the employee with emp_id = 'E101'.
```sql
SELECT * FROM issued_status
WHERE issued_emp_id = 'E101'
```


**Task 5: List Members Who Have Issued More Than One Book**
-- Objective: Use GROUP BY to find members who have issued more than one book.

```sql
SELECT issued_member_id, COUNT(issued_id) AS book_count
FROM issued_status 
GROUP BY issued_member_id
HAVING COUNT(issued_id) >1 ;  
```

### 3. CTAS (Create Table As Select)

- **Task 6: Create Summary Tables**: Used CTAS to generate new tables based on query results - each book and total book_issued_count

```sql
CREATE TABLE book_issued_count AS 
SELECT issued_book_isbn, issued_book_name, COUNT(issued_id) AS total_book_issued_count
FROM issued_status
GROUP BY 1,2;

SELECT * FROM book_issued_count;

```


### 4. Data Analysis & Findings

The following SQL queries were used to address specific questions:

**Task 7. Retrieve All Books in a Specific Category**:

```sql
SELECT * FROM books
WHERE category = 'Classic';
```

**Task 8: Find Total Rental Income by Category**:

```sql
SELECT 
	b.category,
	SUM(b.rental_price) AS total_rental_income,
	COUNT(isbn)
FROM books AS b
JOIN issued_status AS ist
ON b.isbn=ist.issued_book_isbn
GROUP BY 1;
```

**Task 9: List Members Who Registered in the Last 180 Days**:
```sql
INSERT INTO members VALUES ('C111','Sam Bahadur','145 Main St','2025-10-12');
INSERT INTO members VALUES ('C112','John Cena','155 Main St','2025-09-12');

SELECT * FROM members
WHERE reg_date >= CURRENT_DATE - INTERVAL '180 days';
```

**Task 10: List Employees with Their Branch Manager's Name and their branch details**:

```sql
SELECT e1.emp_id,
	e1.emp_name,
	b.*,
	e2.emp_name AS manager_name
FROM employees AS e1
JOIN branch AS b
ON e1.branch_id=b.branch_id
JOIN employees AS e2
ON e2.emp_id = b.manager_id;
```

**Task 11: Create a Table of Books with Rental Price Above a Certain Threshold 7 USD**:
```sql
CREATE TABLE book_price_gt_seven AS
SELECT *
FROM books
WHERE rental_price > 7;

SELECT * FROM book_price_gt_seven;
```

**Task 12: Retrieve the List of Books Not Yet Returned**
```sql
SELECT 
	DISTINCT ist.issued_book_name
FROM issued_status AS ist
LEFT JOIN
return_status AS rst
ON ist.issued_id=rst.issued_id
WHERE rst.return_id IS NULL;
```

## Conclusion

This project demonstrates the application of SQL skills in creating and managing a library management system. It includes database setup, data manipulation, and querying, providing a solid foundation for data management and analysis.






