# **Database Design – Approach**

Database design is an iterative process involving three main phases:
1. **Conceptual database design** – Defining the high-level structure of the database.
2. **Logical database design** – Mapping the conceptual model into a relational schema.
3. **Physical database design** – Optimizing the database for performance and storage.

---

## **1️⃣ Conceptual Database Design**
The first step is to create a **conceptual model** independent of physical considerations. This is typically represented as a **Entity-Relationship (ER) model**.

### **Key Tasks**
- Identifying **entities** and **relationships**.
- Defining **attributes** and their **domains**.
- Determining **candidate, primary, and alternate keys (any candidate key not selected as the primary key)**.

---

## **2️⃣ Logical Database Design**
This step maps the conceptual model into a **relational model**. It is still **independent of the DBMS**, focusing on:

### **Key Tasks**
- **Converting ER model** to relations (tables).
- **Validating relations** using normalization (1NF, 2NF, 3NF).
- **Defining integrity constraints**, such as:
  - **Primary keys** (ensuring uniqueness).
  - **Foreign keys** (enforcing referential integrity).
  - **Business rules** (e.g. max. number of players in game registration).

---

## **3️⃣ Physical Database Design**
This step translates the **logical model** into a **DBMS-specific schema** and optimizes it for **performance** and **storage efficiency**.  
> **Logical database design answers the "what" question, whereas physical design answers "how".**

### **Key Considerations**
- **Base relations (tables)** – How they are implemented in the target DBMS.
- **Indexes and file organization** – Ensuring fast query performance.
- **Security and access control** – Managing data privacy
- **Storage considerations** – Optimizing disk usage.

---

## 📌**Translating Logical Data Model for the Target DBMS**
### **Base Relations**
Before creating tables, check **DBMS-specific features**:
- **Primary, foreign, and alternate keys** – Ensuring data integrity.
- **NOT NULL constraints** – Enforcing required fields.
- **Data types and constraints** – Selecting optimal types (e.g., `VARCHAR(255)` vs. `TEXT`).
- **Integrity constraints** – Ensuring consistent relationships.

### **Managing Derived Data**
**Derived attributes** (e.g., `total_sales`, `average_rating`) can either be:
1. **Stored in the database** (faster retrieval, but requires maintenance).
2. **Calculated on demand** (less storage, but higher query cost).

💡 **Tradeoff:** Choose storage **if queries frequently require derived data** and the computation is expensive

### **Enforcing General Constraints**
Not all business rules can be enforced using simple constraints. Some may require:
- **CHECK constraints** (if supported by the DBMS).
- **Triggers** (to enforce complex business rules).
- **Application logic** (if constraints are difficult to enforce in SQL).

**Example:** Preventing a staff member from handling more than 100 properties
```sql
CONSTRAINT StaffNotHandlingTooMuch
CHECK (NOT EXISTS (
    SELECT staffNo FROM PropertyForRent 
    GROUP BY staffNo HAVING COUNT(*) > 100
))
```
**If the DBMS does not support CHECK constraints, a trigger can be used instead.** General constraints can be also implemented in the application. 

---

## 📌**Designing File Organizations**
The choice of **file organization** determines how data is **physically stored**. Options include:
- **Heap (unordered)** – Fast inserts, but slow searches.
- **Hash** – Efficient for **point lookups**, poor for range queries.
- **Indexed Sequential Access Method (ISAM)** – Balanced search performance.
- **B+-Tree** – **Most common** in modern databases (MySQL InnoDB).
- **Clusters** – Improves retrieval when related rows are frequently accessed together.

💡 **Choosing the right file organization depends on the query workload.** Some DBMSs may not allow selection of file organizations.

---

## 📌**Indexes: Improving Query Performance**
### **What Are Indexes?**
Indexes are **data structures** that speed up searches by reducing the number of disk accesses required. Instead of scanning an entire table, an index allows the DBMS to quickly locate the required rows.

### **Types of Indexes in MySQL**
| **Index Type** | **Description** |
|--------------|----------------|
| **Primary Index** | Automatically created on the `PRIMARY KEY`. Ensures **unique** and **ordered** storage. |
| **Unique Index** | Enforces **uniqueness**, but does not order data. |
| **Secondary Index** | Used for **fast lookups on non-key columns** (e.g., indexing `email` when `id` is the primary key). |
| **Full-Text Index** | Used for **text search**, common in search engines. |

### **Creating Indexes in MySQL**
```sql
-- Primary index (automatically created)
CREATE TABLE Players (
    player_id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL
);

-- Secondary index (speeds up queries on username)
CREATE INDEX idx_username ON Players(username);

-- Multi-column index (improves searches on multiple columns)
CREATE INDEX idx_email_rank ON Players(email, ranking);
```

**Improve performance of queries with indexes**
The best way to improve the performance of SELECT operations is to create indexes on one or more of the columns that are tested in the query. The index entries act like pointers to the table rows, allowing the query to quickly determine which rows match a condition in the WHERE clause, and retrieve the other column values for those rows.

**We must balance the use of indexes**
Although it can be tempting to create an indexes for every possible column used in a query, unnecessary indexes waste space and waste time for the database to determine which indexes to use. Indexes also add to the cost of inserts, updates, and deletes because each index must be updated. You must find the right balance to achieve fast queries using the optimal set of indexes. In other words, we must determine whether adding indexes will improve the performance of the system.

**B-tree is the most used data structure for indexes in the MySQL InnoDB engine**.
[See examples of B+-Trees operations](https://github.com/Tine-m/physical-design/blob/main/Bplus-tree-operations.md)

Indexes are less important for queries on small tables, or big tables where report queries process most or all of the rows. When a query needs to access most of the rows, reading sequentially is faster than working through an index. Sequential reads minimize disk seeks, even if not all the rows are needed for the query. 

**Hints about indexes**
- No index on small tables (everything can stay in memory))
- Always index on PK  
- Index on FKs used frequently
- Index on attributes used in queries and sorting (selection criteria, join kriteria, ORDER BY, GROUP BY and DISTINCT…)
- No  index on attributtes which are frequently updated 
- No index on long text strings 
- Index can cover multiple columnes

💡 **Choosing the right index improves performance, but excessive indexes increase storage cost and slow down updates.**

---

## 📌**Security and Access Control** 
Design the security measures for the database. Relational DBMSs typically provide facilities for:
- System security
- Data security

**System security** covers access and use of the database at the system level, such as a user name and password. 
**Data security** covers access to database objects such as tables and views (use GRANT and REVOKE statements to set up priviliges).

---

## 📌**Storage considerations**
It is a whole topic in itself to plan database capacity and not a mandatory part of the course. 

You may look at the following links to get high level feeling for the extent of the topic:
- [Capacity planning - IBM](https://www.ibm.com/docs/en/storage-protect/8.1.25?topic=server-capacity-planning)
- [SQL Server Capacity Planning | SQL Governor](https://www.sqlgovernor.com/en/sql-server-capacity-planning)
- [Database Capacity Planning - Oracle](https://docs.oracle.com/en-us/iaas/operations-insights/doc/database-capacity-planning.html)
