# **Controlled Redundancy & Denormalization**
Normalization ensures **data integrity**, but it may slow down performance. 
**Denormalization** **(controlled redundancy)** is sometimes used to speed up queries by storing **precomputed** or **duplicated** data. Denormalization introduces redundancy in a controlled by relaxing the normalization rules. As a rule of thumb, if performance is unsatisfactory,and a table has a low update rate and a very high query rate, denormalization may be an viable option.

Denormalization is often used to improve **query performance** by **reducing the number of joins**, at the cost of some **data redundancy**.

---

## **📌 Example 1: Storing Aggregated Values (precomputed totals)**
### **Scenario:** E-commerce system with `Orders` and `OrderDetails`
### **Normalized Schema (3NF)**
```sql
CREATE TABLE Orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE
);

CREATE TABLE OrderDetails (
    order_detail_id INT PRIMARY KEY,
    order_id INT,
    product_id INT,
    quantity INT,
    price DECIMAL(10,2),
    FOREIGN KEY (order_id) REFERENCES Orders(order_id)
);
```
📌 **Problem:** Every time we need the total order amount, we must use `JOIN` and `SUM()`, which can be slow.

### **Denormalized Schema (Adding a redundant `total_amount` column)**
```sql
ALTER TABLE Orders ADD COLUMN total_amount DECIMAL(10,2);

UPDATE Orders o
SET total_amount = (
    SELECT SUM(quantity * price) 
    FROM OrderDetails 
    WHERE order_id = o.order_id
);
```
📌 **Benefit:** Queries like `SELECT total_amount FROM Orders WHERE order_id = 123;` are much faster.

📌 **Drawback:** If an order detail is updated, the `total_amount` must be **manually updated**.

---

## **📌 Example 2: Reducing Joins (storing customer information in orders)**
### **Scenario:** Order processing system with `Customers` and `Orders`
### **Normalized Schema (3NF)**
```sql
CREATE TABLE Customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    phone VARCHAR(20)
);

CREATE TABLE Orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES Customers(customer_id)
);
```
📌 **Problem:** Fetching order details along with customer information requires a `JOIN`, which can be slow.

### **Denormalized Schema (embedding customer information in orders)**
```sql
ALTER TABLE Orders ADD COLUMN customer_name VARCHAR(100);
ALTER TABLE Orders ADD COLUMN customer_email VARCHAR(100);

UPDATE Orders o
JOIN Customers c ON o.customer_id = c.customer_id
SET o.customer_name = c.name, o.customer_email = c.email;
```
📌 **Benefit:** The query `SELECT order_id, customer_name FROM Orders;` runs **faster** because it doesn't require a join.

📌 **Drawback:** **Redundancy** – If the customer's email changes, it must be **updated in multiple places**.

---

## **📌 Example 3: Precomputed Relationships (flattening many-to-many tables)**
### **Scenario:** A `Students` table and a `Courses` table linked via `Enrollments`
### **Normalized Schema (3NF)**
```sql
CREATE TABLE Students (
    student_id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE Courses (
    course_id INT PRIMARY KEY,
    course_name VARCHAR(100)
);

CREATE TABLE Enrollments (
    student_id INT,
    course_id INT,
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES Students(student_id),
    FOREIGN KEY (course_id) REFERENCES Courses(course_id)
);
```
📌 **Problem:** Querying **which courses a student is enrolled in** requires multiple joins.

### **Denormalized Schema (Storing Courses in a Single Column per Student)**
```sql
ALTER TABLE Students ADD COLUMN enrolled_courses TEXT;

UPDATE Students s
SET enrolled_courses = (
    SELECT GROUP_CONCAT(c.course_name SEPARATOR ', ')
    FROM Enrollments e
    JOIN Courses c ON e.course_id = c.course_id
    WHERE e.student_id = s.student_id
);
```
📌 **Benefit:** A **single query** `SELECT enrolled_courses FROM Students WHERE student_id = 1;` fetches all courses.

📌 **Drawback:** **Text fields are harder to search**, and updates to course enrollments must also update the `Students` table.

---

## **🔹 When to Use Denormalization?**
✅ When **joins are causing performance issues**.  
✅ When **read-heavy queries need optimization**.  
✅ When **data updates are rare but reads are frequent**.  
