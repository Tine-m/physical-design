# **📌 Exercises: Practicing Denormalization and Partitioning**

These exercises will help students understand when and how to **denormalize a database** and when **partitioning** can improve performance.

---

## **📌 Scenario: Video Streaming Service**

You are designing a **database for a video streaming platform** that handles **millions of user interactions daily**. The system must be optimized for **fast data retrieval** and **efficient data management**.

The system includes:
- **Users** who watch videos.
- **Videos** with metadata and playback information.
- **WatchHistory** storing user interactions with videos.
- **Subscriptions** tracking user payment and plan type.

---

## **📝 Exercise 1: Denormalizing Video Views Count**

### **🎯 Problem Statement**
Currently, the database stores each **video watch event separately**. Retrieving the **total views of a video** requires a **`COUNT()` query**, which is slow.

### **✅ Task**
1. Modify the **`Videos`** table to store a **precomputed total views** count.
2. Write an **SQL statement** to update this field.
3. Discuss the **pros and cons** of this denormalization.

### **✅ Normalized Schema (Before)**
```sql
CREATE TABLE Videos (
    video_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255),
    upload_date DATE
);

CREATE TABLE WatchHistory (
    watch_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    video_id INT,
    watched_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (video_id) REFERENCES Videos(video_id)
);
```

### **✅ Denormalized Schema (After)**
```sql
ALTER TABLE Videos ADD COLUMN total_views INT DEFAULT 0;

UPDATE Videos v
SET total_views = (
    SELECT COUNT(*) FROM WatchHistory WHERE video_id = v.video_id
);
```

### **📌 Discussion Questions**
- What are the **performance benefits** of this approach?
- How should we ensure **total_views** stays **accurate** when new views are added?

---

## **📝 Exercise 2: Denormalizing Subscription Information in Users Table**

### **🎯 Problem Statement**
User subscription details (e.g., plan type, expiration date) are stored in a **separate `Subscriptions` table**, requiring a **join** to fetch user details along with subscription data.

### **✅ Task**
1. Modify the `Users` table to **embed subscription details**.
2. Write an **UPDATE statement** to copy data from `Subscriptions` to `Users`.
3. Discuss the **downsides** of this approach.

### **✅ Normalized Schema (Before)**
```sql
CREATE TABLE Users (
    user_id INT PRIMARY KEY,
    username VARCHAR(100),
    email VARCHAR(100)
);

CREATE TABLE Subscriptions (
    subscription_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    plan_type VARCHAR(50),
    expiration_date DATE,
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);
```

### **✅ Denormalized Schema (After)**
```sql
ALTER TABLE Users ADD COLUMN plan_type VARCHAR(50);
ALTER TABLE Users ADD COLUMN subscription_expiration DATE;

UPDATE Users u
JOIN Subscriptions s ON u.user_id = s.user_id
SET u.plan_type = s.plan_type, u.subscription_expiration = s.expiration_date;
```

### **📌 Discussion Questions**
- When would this **denormalization** be useful?
- How should updates to `Subscriptions` be **handled** in this case?

---

## **📝 Exercise 3: Flattening User Preferences for Faster Access**

### **🎯 Problem Statement**
Users can have multiple **viewing preferences** (e.g., genres, resolution preferences, preferred languages). Storing these in a separate table results in **slow queries requiring joins**.

### **✅ Task**
1. Modify the `Users` table to **store preferences as a comma-separated string**.
2. Write an **UPDATE statement** to copy data from `UserPreferences` to `Users`.
3. Discuss the trade-offs of this approach.

### **✅ Normalized Schema (Before)**
```sql
CREATE TABLE Users (
    user_id INT PRIMARY KEY,
    username VARCHAR(100)
);

CREATE TABLE UserPreferences (
    user_id INT,
    preference VARCHAR(100),
    PRIMARY KEY (user_id, preference),
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);
```

### **✅ Denormalized Schema (After)**
```sql
ALTER TABLE Users ADD COLUMN preferences TEXT;

UPDATE Users u
SET preferences = (
    SELECT GROUP_CONCAT(preference SEPARATOR ', ')
    FROM UserPreferences
    WHERE user_id = u.user_id
);
```

### **📌 Discussion Questions**
- What are the **downsides** of storing preferences in a single column?
- How would you handle **searching within this field efficiently**?

---

## **📝 Exercise 4: Using Partitioning for Video Playback Data**

### **🎯 Problem Statement**
The **`PlaybackLogs`** table has **millions of rows**, and querying data for a single **month** is slow.

### **✅ Task**
1. Modify the `PlaybackLogs` table to use **partitioning by month**.
2. Write a **query** that benefits from partitioning.
3. Discuss when **partitioning is useful**.

### **✅ Normalized Schema (Before)**
```sql
CREATE TABLE PlaybackLogs (
    log_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    video_id INT,
    play_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### **✅ Partitioned Schema (After)**
```sql
CREATE TABLE PlaybackLogs (
    log_id INT NOT NULL,
    user_id INT NOT NULL,
    video_id INT NOT NULL,
    play_time TIMESTAMP NOT NULL
)
PARTITION BY RANGE (TO_DAYS(play_time)) (
    PARTITION p202401 VALUES LESS THAN (TO_DAYS('2024-02-01')),
    PARTITION p202402 VALUES LESS THAN (TO_DAYS('2024-03-01')),
    PARTITION p202403 VALUES LESS THAN (TO_DAYS('2024-04-01'))
);
```

### **✅ Query Example (Efficient Query on Partitioned Table)**
```sql
SELECT * FROM PlaybackLogs PARTITION (p202402) WHERE user_id = 1001;
```

### **📌 Discussion Questions**
- How does **partitioning** improve query speed?
- What happens when a **new month starts**?

---

## **📌 Key Learning Outcomes**
By completing these exercises, students will:
✅ Learn when **denormalization improves performance**.  
✅ Understand how **precomputed values** speed up queries.  
✅ Apply **partitioning** techniques for **scalable database design**.  
✅ Discuss **trade-offs** between **normalization, denormalization, and partitioning**.  

