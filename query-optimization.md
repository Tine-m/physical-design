**Query Optimization Techniques**
### **Best Practices**
✅ Avoid `SELECT *`, query only required columns.  
✅ Use **EXISTS** instead of `IN` for subqueries.  
✅ Optimize joins (`EXPLAIN` to check execution plan).  

📌 **Example: Optimized Query**
```sql
SELECT * FROM orders o WHERE EXISTS (
    SELECT 1 FROM vip_customers v WHERE o.customer_id = v.customer_id
);
```
---
