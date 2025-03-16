## **📌 Query Optimization Exercises**

### **🔹 Exercise 1: Optimizing a Subquery into a Join**

#### **Task:**  
The following query is slow because it uses a subquery. Rewrite it as a **JOIN** to improve performance.

```sql
SELECT order_id, total_amount,
       (SELECT name FROM Customers WHERE customer_id = Orders.customer_id) AS customer_name
FROM Orders
WHERE total_amount > 100;
```

✅ **Goal:**  
- Convert the query to **use a JOIN**.
- Use `EXPLAIN` to verify **index usage and row scans**.

---

### **🔹 Exercise 2: Optimizing a JOIN by Using an Index**

#### **Task:**  
The following query is slow because the **join condition is missing an index**.  
1. **Check its execution plan (`EXPLAIN`)**.
2. **Create an appropriate index**.
3. **Rerun the query and compare performance**.

```sql
SELECT o.order_id, o.total_amount, c.name
FROM Orders o
JOIN Customers c ON o.customer_id = c.customer_id
WHERE o.order_date > '2023-01-01';
```

✅ **Goal:**  
- Create an index on `Orders.customer_id`.
- Compare **query performance before and after indexing**.

---

### **🔹 Exercise 3: Identifying and Fixing the N+1 Query Problem**

#### **Task:**  
Below is an inefficient way of retrieving data that causes an **N+1 Query Problem**.

```sql
-- Fetch all orders first
SELECT order_id, customer_id FROM Orders WHERE order_date > '2023-01-01';

-- Then, for each order, fetch customer name separately
SELECT name FROM Customers WHERE customer_id = 1;
SELECT name FROM Customers WHERE customer_id = 2;
SELECT name FROM Customers WHERE customer_id = 3;
...
```

✅ **Goal:**  
- Identify why the **N+1 Query Problem happens**.
- Rewrite it **using a single JOIN query**.

---

## **📌 Summary: Best Practices**

| Query Type  | Performance | Best Use Case |
|------------|------------|----------------|
| ✅ **JOIN** | **Fast** 🚀 | When retrieving related data from multiple tables |
| ❌ **Subquery** | **Slow (if correlated)** 🐢 | Only when aggregating unique results |
| ❌ **N+1 Problem** | **Very Slow** 🔥 | Fix it with **joins** or **batch queries** |

---

Would you like additional **query optimization exercises** with more complex data structures? 🚀😊
