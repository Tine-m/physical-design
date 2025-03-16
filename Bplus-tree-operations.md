# **B+-Tree Operations**

A **B+-Tree** is a **self-balancing tree** commonly used in databases to organize indexes efficiently. It is optimized for **fast searches, insertions, and deletions** while maintaining a balanced structure.

### **🔹 Key Properties of a B+-Tree**
✔ **Balanced Structure** – All leaf nodes are at the same level.

✔ **Internal Nodes Store Keys Only** – Actual data is stored in leaf nodes.

✔ **Leaf Nodes Are Linked** – Supports efficient range queries.

✔ **Efficient Disk Reads** – Reduces access time for large datasets.

---

# **1️⃣ Visualizing Insertions in a B+-Tree**

### **📌 Step 1: Insert 10**
```
  [ 10 ]
(Leaf Node)
```


---
### **📌 Step 2: Insert 20, 30**
```
  [ 10   20   30 ]
(Leaf Node)
```



📌 **No split needed since the node has space.**

---
### **📌 Step 3: Insert 40 (Leaf Node Split)**
```
         [ 20 ]
       /      \
 [10]  [20,30,40]  (Leaf Nodes)
```


📌 **Leaf node exceeded max keys (3), causing a split.**
📌 **Middle value (20) moves up to the parent node.**

---
### **📌 Step 4: Insert 50, 60, 70 (Another Split Occurs)**
```
         [ 20   40 ]
       /     |      \
 [10]   [20,30]   [40,50,60,70]
```


📌 **Further splitting occurs to maintain balance.**

---

# **2️⃣ Visualizing Deletions in a B+-Tree**

### **📌 Step 5: Delete 70 (No Underflow)**
```
         [ 20   40 ]
       /     |      \
 [10]   [20,30]   [40,50,60]
```


📌 **Leaf node still has enough keys, so no merging is required.**

---
### **📌 Step 6: Delete 40 (Internal Node Key Removed)**
```
         [ 20   50 ]
       /     |      \
 [10]   [20,30]   [50,60]
```


📌 **Internal node key (40) is removed, replaced with successor (50).**

---
### **📌 Step 7: Delete 50 (Causes Merging)**
```
         [ 20 ]
       /      \
 [10]   [20,30,60]
```


📌 **Since `50` was a parent node, merging was necessary.**
📌 **B+-Tree structure remains balanced.**

---

# **🔹 Summary of B+-Tree Operations**
| **Operation** | **What Happens?** |
|--------------|------------------|
| **Insertion** | Adds key to leaf node. If full, splits and promotes middle key. |
| **Deletion (Leaf Node)** | If enough keys remain, simple removal. If underflow, borrow from neighbor or merge. |
| **Deletion (Internal Node)** | Replace with **successor key** from right subtree. |

💡 **B+-Trees dynamically restructure themselves to maintain efficiency.**

