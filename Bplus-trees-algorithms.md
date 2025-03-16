# **B+-Tree Insertions, Deletions & Search**

## **📌 Introduction**
A **B+-Tree** is a **self-balancing search tree** used in databases to **efficiently store and retrieve data**. The key operations include **insertions** and **deletions**, which maintain balance while minimizing disk I/O.

---

# **1️⃣ B+-Tree Insertion Algorithm**
### **Goal: Insert a key while keeping the tree balanced.**
Each node can have **at most `m` keys** (where `m` is the order of the B+-Tree). If a node **overflows**, it splits.

---

## **🔹 Algorithm Steps for Insertion**
1. **Find the Correct Leaf Node**
   - Start at the **root** and traverse down.
   - Choose the **appropriate child** by comparing keys.
   - Continue **until reaching a leaf node**.

2. **Insert the Key into the Leaf Node**
   - Place the key in **sorted order** in the leaf node.
   - If the leaf **has space**, the operation is **complete**.
   - Otherwise, **split occurs**.

3. **Handle Overflow by Splitting (if needed)**
   - If the leaf node **exceeds its maximum capacity (`m-1` keys)**:
     - **Split the node into two halves**.
     - Push the **middle key** up to the parent.
     - Adjust the **pointers** accordingly.

4. **Repeat Splitting Up the Tree (if needed)**
   - If the parent **overflows**, it splits too.
   - This process **propagates upwards** until the tree stabilizes.
   - If the root splits, **a new root is created**, increasing tree height.

---

## **🔹 Example of Insertion (B+-Tree of Order `3`)**
#### **📌 Insert keys: 10, 20, 30, 40**
```
Step 1: Insert 10, 20, 30 (Fits in one leaf)
  [ 10   20   30 ]
(Leaf Node)
```
📌 **No split needed yet.**

```
Step 2: Insert 40 (Overflow → Split Occurs)
         [ 20 ]
       /      \
 [10]  [20,30,40]
```
📌 **Middle key `20` moves up to the parent.**

---

# **2️⃣ B+-Tree Deletion Algorithm**
### **Goal: Remove a key while maintaining balance.**
Each node must have **at least ⌈m/2⌉ keys** to prevent underflow. If a node **underflows**, it borrows or merges.

---

## **🔹 Algorithm Steps for Deletion**
1. **Find the Key in the Tree**
   - Start at the **root** and traverse down.
   - Locate the **leaf node** that contains the key.

2. **Delete the Key from the Leaf Node**
   - Remove the key.
   - If the leaf node **still has enough keys**, stop.
   - If the node **underflows**, rebalance.

3. **Handle Underflow by Borrowing (if needed)**
   - If a node **has too few keys** (less than ⌈m/2⌉):
     - **Borrow from a neighboring sibling**.
     - Move a **key from parent to maintain balance**.

4. **Handle Merging (if borrowing is not possible)**
   - If borrowing **isn’t possible**, **merge with a sibling**.
   - The **parent key moves down**, merging the nodes.
   - If the parent underflows, **repeat up the tree**.
   - If the root is empty, **reduce tree height**.

---

## **🔹 Example of Deletion (B+-Tree of Order `3`)**
#### **📌 Delete key `40`**
```
Before:
         [ 20   40 ]
       /     |      \
 [10]   [20,30]   [40,50,60]
```
📌 **`40` is removed, and `50` replaces it in the parent.**
```
After:
         [ 20   50 ]
       /     |      \
 [10]   [20,30]   [50,60]
```

#### **📌 Delete key `50` (Causes Merging)**
```
Before:
         [ 20   50 ]
       /     |      \
 [10]   [20,30]   [50,60]
```
📌 **`50` is removed, causing a merge.**
```
After:
         [ 20 ]
       /      \
 [10]   [20,30,60]
```
📌 **The tree height shrinks.**

---

---

# **3️⃣ B+-Tree Search Algorithm**
### **Goal: Find a key efficiently in O(log n) time.**

## **🔹 Algorithm Steps for Searching**
1. **Start from the Root Node**
   - Compare the target key with keys in the root.
   - Choose the correct child node and traverse down.

2. **Repeat Until a Leaf Node is Reached**
   - At each level, choose the correct branch.
   - If the key is found at an internal node, continue searching in the leaf nodes.

3. **Return the Search Result**
   - If found, return the pointer to the data.
   - If not found, return NULL or indicate the absence of the key.

---

## **🔹 Example of Searching for `30`**
```
         [ 20   50 ]
       /     |      \
 [10]   [20,30]   [50,60]
```
📌 **Search for `30`:**
   - Start at `20, 50` → `30` is greater than `20`, so move to the right.
   - Reach `[20, 30]` → `30` is found!
   - Return pointer to data in the database.

---

# **4️⃣ Time Complexity Analysis**
| **Operation** | **Time Complexity** |
|--------------|------------------|
| **Search** | O(log n) |
| **Insertion** | O(log n) (split may propagate upwards) |
| **Deletion** | O(log n) (merging may propagate upwards) |

📌 **Why is B+-Tree efficient?**  
- **Balanced structure** keeps operations in **O(log n)**.
- **Leaf node linking** makes **range queries efficient**.
- **Minimizes disk I/O** in large-scale databases.

---

## **Conclusion**
✔ **B+-Trees ensure efficient insertions, deletions, and searches** while keeping search time logarithmic.  
✔ **Splitting and merging maintain balance, preventing deep tree structures.**  
✔ **Used in MySQL InnoDB indexes and many other databases for efficient query execution.**
