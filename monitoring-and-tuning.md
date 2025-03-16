## **🔹 Controlled Redundancy & Denormalization**
Normalization ensures **data integrity**, but it may slow down performance. **Denormalization** **(controlled redundancy)** is sometimes used to speed up queries by storing **precomputed** or **duplicated** data.

### **Example: Adding `total_sales` in `Customers` to avoid recalculating it from `Orders` every time.**
```sql
ALTER TABLE Customers ADD COLUMN total_sales DECIMAL(10,2);
```
📌 **Denormalization improves reads but makes updates more complex.**

---

# **Monitoring & Tuning**
## **🔹 Monitoring MySQL Performance**
1. **Use `EXPLAIN` to analyze query execution:**
   ```sql
   EXPLAIN SELECT * FROM Players WHERE username = 'Player123';
   ```
2. **Monitor slow queries (`slow_query_log` in MySQL).**
3. **Use MySQL Workbench performance dashboards.**
