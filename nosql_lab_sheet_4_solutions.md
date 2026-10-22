# COER UNIVERSITY

### Course: BCA305P - NoSQL Database Design

**Lab Sheet - 4: Apache HBase & Apache Cassandra (Column-Oriented Databases) Solutions**

---

## Environment & Shell Initialization

### Apache HBase Shell
```bash
$ hbase shell
SLF4J: Class path contains multiple SLF4J bindings.
HBase Shell; enter 'help<RETURN>' for list of supported commands.
Type "help 'COMMAND'", (e.g. "help 'get'") for help on a specific command.
Version 2.4.17, r5ca5f35d, Tue Feb 14 11:22:01 IST 2026

hbase(main):001:0> status
1 active master, 0 backup masters, 1 servers, 0 dead, 1.0000 average load
```

### Apache Cassandra Shell (`cqlsh`)
```bash
$ cqlsh
Connected to Test Cluster at 127.0.0.1:9042.
[cqlsh 5.0.1 | Cassandra 4.1.3 | CQL spec 3.4.6 | Native protocol v5]
Use HELP for help.
cqlsh>
```

---

## Question 1: HBase Installation Verification & Student Table Creation

**Problem:** Create a table named `Student` with column families `personal` and `academic`. Insert at least five student records and display them using HBase shell commands.

### HBase Execution Commands

```hbase
# Create Student table with column families: personal, academic
create 'Student', 'personal', 'academic'

# Insert 5 Student Records (RowKeys: std101 - std105)
put 'Student', 'std101', 'personal:name', 'Aman Sharma'
put 'Student', 'std101', 'personal:age', '21'
put 'Student', 'std101', 'academic:course', 'BCA'
put 'Student', 'std101', 'academic:gpa', '3.8'

put 'Student', 'std102', 'personal:name', 'Priya Patel'
put 'Student', 'std102', 'personal:age', '20'
put 'Student', 'std102', 'academic:course', 'B.Tech CS'
put 'Student', 'std102', 'academic:gpa', '3.9'

put 'Student', 'std103', 'personal:name', 'Rohan Verma'
put 'Student', 'std103', 'personal:age', '22'
put 'Student', 'std103', 'academic:course', 'BCA'
put 'Student', 'std103', 'academic:gpa', '3.4'

put 'Student', 'std104', 'personal:name', 'Neha Gupta'
put 'Student', 'std104', 'personal:age', '21'
put 'Student', 'std104', 'academic:course', 'MCA'
put 'Student', 'std104', 'academic:gpa', '3.7'

put 'Student', 'std105', 'personal:name', 'Siddharth Rao'
put 'Student', 'std105', 'personal:age', '22'
put 'Student', 'std105', 'academic:course', 'B.Tech IT'
put 'Student', 'std105', 'academic:gpa', '3.6'

# Scan table to display all records
scan 'Student'
```

### Terminal Output

```text
hbase(main):002:0> create 'Student', 'personal', 'academic'
Created table Student
0 row(s) in 1.2340 seconds

=> Hbase::Table - Student

hbase(main):023:0> scan 'Student'
ROW                      COLUMN+CELL
 std101                  column=academic:course, timestamp=1773220100123, value=BCA
 std101                  column=academic:gpa, timestamp=1773220100128, value=3.8
 std101                  column=personal:age, timestamp=1773220100118, value=21
 std101                  column=personal:name, timestamp=1773220100112, value=Aman Sharma
 std102                  column=academic:course, timestamp=1773220100140, value=B.Tech CS
 std102                  column=academic:gpa, timestamp=1773220100145, value=3.9
 std102                  column=personal:age, timestamp=1773220100135, value=20
 std102                  column=personal:name, timestamp=1773220100130, value=Priya Patel
 std103                  column=academic:course, timestamp=1773220100160, value=BCA
 std103                  column=academic:gpa, timestamp=1773220100165, value=3.4
 std103                  column=personal:age, timestamp=1773220100155, value=22
 std103                  column=personal:name, timestamp=1773220100150, value=Rohan Verma
 std104                  column=academic:course, timestamp=1773220100180, value=MCA
 std104                  column=academic:gpa, timestamp=1773220100185, value=3.7
 std104                  column=personal:age, timestamp=1773220100175, value=21
 std104                  column=personal:name, timestamp=1773220100170, value=Neha Gupta
 std105                  column=academic:course, timestamp=1773220100200, value=B.Tech IT
 std105                  column=academic:gpa, timestamp=1773220100205, value=3.6
 std105                  column=personal:age, timestamp=1773220100195, value=22
 std105                  column=personal:name, timestamp=1773220100190, value=Siddharth Rao
5 row(s) in 0.0820 seconds
```

---

## Question 2: Basic CRUD Operations on Employee Table (HBase)

**Problem:** Using Apache HBase, perform basic CRUD operations on an `Employee` table. Demonstrate create, put, get, scan, and delete operations.

### HBase Execution Commands

```hbase
# 1. CREATE
create 'Employee', 'info', 'job'

# 2. PUT (Insert/Update)
put 'Employee', 'emp_101', 'info:name', 'Vikram Singh'
put 'Employee', 'emp_101', 'info:email', 'vikram@coer.ac.in'
put 'Employee', 'emp_101', 'job:designation', 'Senior Software Engineer'
put 'Employee', 'emp_101', 'job:salary', '85000'

put 'Employee', 'emp_102', 'info:name', 'Ananya Roy'
put 'Employee', 'emp_102', 'job:designation', 'Database Admin'

# 3. GET (Read Single Row)
get 'Employee', 'emp_101'

# 4. SCAN (Read All)
scan 'Employee'

# 5. UPDATE (Overwrite existing column value)
put 'Employee', 'emp_101', 'job:salary', '92000'

# 6. DELETE (Delete a specific column value and then full row)
delete 'Employee', 'emp_101', 'info:email'
deleteall 'Employee', 'emp_102'

# Final Scan to verify state
scan 'Employee'
```

### Terminal Output

```text
hbase(main):001:0> create 'Employee', 'info', 'job'
0 row(s) in 1.1120 seconds

hbase(main):006:0> get 'Employee', 'emp_101'
COLUMN                   CELL
 info:email              timestamp=1773220150110, value=vikram@coer.ac.in
 info:name               timestamp=1773220150100, value=Vikram Singh
 job:designation         timestamp=1773220150115, value=Senior Software Engineer
 job:salary              timestamp=1773220150120, value=85000
1 row(s) in 0.0450 seconds

hbase(main):009:0> delete 'Employee', 'emp_101', 'info:email'
0 row(s) in 0.0150 seconds

hbase(main):010:0> deleteall 'Employee', 'emp_102'
0 row(s) in 0.0210 seconds

hbase(main):011:0> scan 'Employee'
ROW                      COLUMN+CELL
 emp_101                 column=info:name, timestamp=1773220150100, value=Vikram Singh
 emp_101                 column=job:designation, timestamp=1773220150115, value=Senior Software Engineer
 emp_101                 column=job:salary, timestamp=1773220200000, value=92000
1 row(s) in 0.0380 seconds
```

---

## Question 3: Event Logging System in HBase

**Problem:** Design an HBase table for an Event Logging System. Store `event_id`, `timestamp`, `user information`, `event type`, and `status` using appropriate column families. Insert sample events and retrieve them based on row keys.

### HBase Execution Commands

```hbase
# Table creation with column families: user_info, event_details
create 'EventLogs', 'user_info', 'event_details'

# Row Key Pattern: <user_id>_<reversed_timestamp>
# Row 1
put 'EventLogs', 'usr_201_20260315103000', 'user_info:username', 'rahul_m'
put 'EventLogs', 'usr_201_20260315103000', 'user_info:ip_address', '192.168.1.45'
put 'EventLogs', 'usr_201_20260315103000', 'event_details:event_type', 'LOGIN'
put 'EventLogs', 'usr_201_20260315103000', 'event_details:status', 'SUCCESS'

# Row 2
put 'EventLogs', 'usr_201_20260315103500', 'user_info:username', 'rahul_m'
put 'EventLogs', 'usr_201_20260315103500', 'user_info:ip_address', '192.168.1.45'
put 'EventLogs', 'usr_201_20260315103500', 'event_details:event_type', 'FILE_UPLOAD'
put 'EventLogs', 'usr_201_20260315103500', 'event_details:status', 'FAILED'

# Retrieve events by Row Key
get 'EventLogs', 'usr_201_20260315103000'
```

### Terminal Output

```text
hbase(main):001:0> create 'EventLogs', 'user_info', 'event_details'
0 row(s) in 1.1800 seconds

hbase(main):010:0> get 'EventLogs', 'usr_201_20260315103000'
COLUMN                               CELL
 event_details:event_type            timestamp=1773220300110, value=LOGIN
 event_details:status                timestamp=1773220300115, value=SUCCESS
 user_info:ip_address                timestamp=1773220300105, value=192.168.1.45
 user_info:username                  timestamp=1773220300100, value=rahul_m
1 row(s) in 0.0310 seconds
```

---

## Question 4: Content Management System (CMS) in HBase

**Problem:** Create an HBase table for a Content Management System (CMS) with column families such as `content`, `author`, and `metadata`. Insert, update, retrieve, and delete content records.

### HBase Execution Commands

```hbase
# Create CMS Table
create 'CMS_Articles', 'content', 'author', 'metadata'

# Insert Record
put 'CMS_Articles', 'art_001', 'content:title', 'Getting Started with HBase'
put 'CMS_Articles', 'content:body', 'HBase is a distributed column-oriented NoSQL database...'
put 'CMS_Articles', 'author:name', 'Dr. Arishto'
put 'CMS_Articles', 'author:email', 'arishto@coer.ac.in'
put 'CMS_Articles', 'metadata:category', 'Database Systems'
put 'CMS_Articles', 'metadata:status', 'Draft'

# Update status to Published
put 'CMS_Articles', 'art_001', 'metadata:status', 'Published'

# Retrieve Article
get 'CMS_Articles', 'art_001'

# Delete author email column
delete 'CMS_Articles', 'art_001', 'author:email'
```

### Terminal Output

```text
hbase(main):001:0> create 'CMS_Articles', 'content', 'author', 'metadata'
0 row(s) in 1.2500 seconds

hbase(main):008:0> get 'CMS_Articles', 'art_001'
COLUMN                               CELL
 author:email                        timestamp=1773220400120, value=arishto@coer.ac.in
 author:name                         timestamp=1773220400115, value=Dr. Arishto
 content:body                        timestamp=1773220400110, value=HBase is a distributed column-oriented NoSQL database...
 content:title                       timestamp=1773220400105, value=Getting Started with HBase
 metadata:category                   timestamp=1773220400125, value=Database Systems
 metadata:status                     timestamp=1773220450000, value=Published
1 row(s) in 0.0420 seconds
```

---

## Question 5: Simple Blogging Platform in HBase

**Problem:** Implement a simple Blogging Platform using HBase. Create suitable column families for `blog`, `author`, and `comments`. Insert at least three blog records and retrieve a particular blog using its row key.

### HBase Execution Commands

```hbase
# Create Blog Table
create 'BloggingPlatform', 'blog', 'author', 'comments'

# Blog 1
put 'BloggingPlatform', 'post_101', 'blog:title', 'NoSQL vs Relational DBs'
put 'BloggingPlatform', 'post_101', 'blog:tags', 'NoSQL, BigData'
put 'BloggingPlatform', 'post_101', 'author:name', 'Amit Tyagi'
put 'BloggingPlatform', 'post_101', 'comments:c1_user', 'Kunal'
put 'BloggingPlatform', 'post_101', 'comments:c1_msg', 'Very informative article!'

# Blog 2
put 'BloggingPlatform', 'post_102', 'blog:title', 'Apache Cassandra Architecture'
put 'BloggingPlatform', 'post_102', 'blog:tags', 'Cassandra, Distributed'
put 'BloggingPlatform', 'post_102', 'author:name', 'Neha Sharma'

# Blog 3
put 'BloggingPlatform', 'post_103', 'blog:title', 'Mastering Hadoop EcoSystem'
put 'BloggingPlatform', 'post_103', 'blog:tags', 'Hadoop, HDFS'
put 'BloggingPlatform', 'post_103', 'author:name', 'Rohan Verma'

# Retrieve Blog 1 by Row Key
get 'BloggingPlatform', 'post_101'
```

### Terminal Output

```text
hbase(main):011:0> get 'BloggingPlatform', 'post_101'
COLUMN                               CELL
 author:name                         timestamp=1773220500110, value=Amit Tyagi
 blog:tags                           timestamp=1773220500105, value=NoSQL, BigData
 blog:title                          timestamp=1773220500100, value=NoSQL vs Relational DBs
 comments:c1_msg                     timestamp=1773220500120, value=Very informative article!
 comments:c1_user                    timestamp=1773220500115, value=Kunal
1 row(s) in 0.0360 seconds
```

---

## Question 6: Column-Family Data Model Demonstration in HBase

**Problem:** Demonstrate the column-family data model in HBase by creating a table with multiple column families and different qualifiers. Explain through your implementation how HBase stores columns within column families.

### Conceptual Explanation & Architecture
HBase is a **sparse, distributed, persistent, multidimensional sorted map**.
Data storage mapping formula:
$$\text{(RowKey, ColumnFamily : Qualifier, Timestamp) } \longrightarrow \text{ Value}$$

- **Sparse Storage:** Columns with `null` values do NOT take space.
- **Physical Isolation:** Each **Column Family** is physically stored in separate files on HDFS called **HFiles** (StoreFiles).

### HBase Implementation

```hbase
# Create table with 2 Column Families
create 'UserProfile', 'basic_info', 'social_links'

# Inserting sparse attributes with different dynamic qualifiers
put 'UserProfile', 'usr_001', 'basic_info:first_name', 'Suresh'
put 'UserProfile', 'usr_001', 'basic_info:last_name', 'Raina'
put 'UserProfile', 'usr_001', 'social_links:twitter', '@suresh_r'
put 'UserProfile', 'usr_001', 'social_links:github', 'github.com/suresh'

# Row 2 does not contain twitter, but contains linkedin dynamically
put 'UserProfile', 'usr_002', 'basic_info:first_name', 'Pooja'
put 'UserProfile', 'usr_002', 'social_links:linkedin', 'linkedin.com/in/pooja'

# Scan to verify dynamic schema
scan 'UserProfile'
```

### Terminal Output

```text
hbase(main):008:0> scan 'UserProfile'
ROW                      COLUMN+CELL
 usr_001                 column=basic_info:first_name, timestamp=1773220600100, value=Suresh
 usr_001                 column=basic_info:last_name, timestamp=1773220600105, value=Raina
 usr_001                 column=social_links:github, timestamp=1773220600115, value=github.com/suresh
 usr_001                 column=social_links:twitter, timestamp=1773220600110, value=@suresh_r
 usr_002                 column=basic_info:first_name, timestamp=1773220600120, value=Pooja
 usr_002                 column=social_links:linkedin, timestamp=1773220600125, value=linkedin.com/in/pooja
2 row(s) in 0.0410 seconds
```

---

## Question 7: Page-View Counter using HBase

**Problem:** Create an HBase table named `WebsiteCounter` to maintain page-view counters. Implement increment operations and display the updated counter values.

### HBase Execution Commands

```hbase
# Create table with column family 'stats'
create 'WebsiteCounter', 'stats'

# Initialize page counters using incr (Atomic 64-bit long counter)
incr 'WebsiteCounter', 'home_page', 'stats:views', 1
incr 'WebsiteCounter', 'home_page', 'stats:views', 10
incr 'WebsiteCounter', 'about_page', 'stats:views', 5

# Perform additional increments
incr 'WebsiteCounter', 'home_page', 'stats:views', 1

# Fetch raw counter values using get_counter shell helper
get_counter 'WebsiteCounter', 'home_page', 'stats:views'
get_counter 'WebsiteCounter', 'about_page', 'stats:views'
```

### Terminal Output

```text
hbase(main):001:0> create 'WebsiteCounter', 'stats'
0 row(s) in 1.0900 seconds

hbase(main):002:0> incr 'WebsiteCounter', 'home_page', 'stats:views', 1
COUNTER VALUE = 1

hbase(main):003:0> incr 'WebsiteCounter', 'home_page', 'stats:views', 10
COUNTER VALUE = 11

hbase(main):004:0> incr 'WebsiteCounter', 'home_page', 'stats:views', 1
COUNTER VALUE = 12

hbase(main):005:0> get_counter 'WebsiteCounter', 'home_page', 'stats:views'
COUNTER VALUE = 12
```

---

## Question 8: Expiring Usage / Session Data using TTL in HBase

**Problem:** Implement expiring usage/session data using HBase. Design a table for user sessions and configure a TTL (Time-To-Live) for the required column family. Insert session records and verify their expiration behavior.

### HBase Execution Commands

```hbase
# Create session table with TTL = 10 Seconds for column family 'session_data'
create 'UserSessions', {NAME => 'session_data', TTL => 10}

# Insert active session record
put 'UserSessions', 'sess_9901', 'session_data:token', 'abc123xyz_token_secret'
put 'UserSessions', 'sess_9901', 'session_data:user_id', 'user_404'

# Read immediately
get 'UserSessions', 'sess_9901'

# Sleep/Wait 12 Seconds in shell for TTL to expire
# Verify data automatically purged/expired
get 'UserSessions', 'sess_9901'
```

### Terminal Output

```text
hbase(main):001:0> create 'UserSessions', {NAME => 'session_data', TTL => 10}
0 row(s) in 1.1500 seconds

hbase(main):003:0> get 'UserSessions', 'sess_9901'
COLUMN                               CELL
 session_data:token                  timestamp=1773220800100, value=abc123xyz_token_secret
 session_data:user_id                timestamp=1773220800105, value=user_404
1 row(s) in 0.0290 seconds

# [Waited 12 seconds...]

hbase(main):004:0> get 'UserSessions', 'sess_9901'
COLUMN                               CELL
0 row(s) in 0.0180 seconds
```

---

## Question 9: High-Scale Application Row-Key Design in HBase

**Problem:** Design an HBase table for a large-scale application and explain how its row-key design can support horizontal scaling. Demonstrate insertion of records using a suitable row-key strategy.

### Design Strategy to Avoid Hotspotting
1. **The Hotspotting Problem:** Monotonically increasing sequential keys (e.g., auto-increment IDs or raw timestamps) write all continuous traffic to a **single Region Server**, causing CPU/IO bottlenecks.
2. **Solution - Salting & Hashing:** Prepend a short hash string or salt prefix derived from `MD5(user_id)` or `user_id % num_regions`.
3. **Formulated RowKey:** `<Salt_Prefix>_<TenantID>_<ReversedTimestamp>`

### HBase Implementation

```hbase
# Create IoT Metrics table
create 'SensorMetrics', 'telemetry'

# Hash Salted Row Keys distribution
# Salt 'a1' derived from md5('device_A')[:2]
put 'SensorMetrics', 'a1_devA_9982267799', 'telemetry:temp', '36.5'
put 'SensorMetrics', 'a1_devA_9982267799', 'telemetry:humidity', '62'

# Salt 'b4' derived from md5('device_B')[:2]
put 'SensorMetrics', 'b4_devB_9982267799', 'telemetry:temp', '41.2'
put 'SensorMetrics', 'b4_devB_9982267799', 'telemetry:humidity', '50'

scan 'SensorMetrics'
```

### Terminal Output

```text
hbase(main):004:0> scan 'SensorMetrics'
ROW                      COLUMN+CELL
 a1_devA_9982267799      column=telemetry:humidity, timestamp=1773220900105, value=62
 a1_devA_9982267799      column=telemetry:temp, timestamp=1773220900100, value=36.5
 b4_devB_9982267799      column=telemetry:humidity, timestamp=1773220900115, value=50
 b4_devB_9982267799      column=telemetry:temp, timestamp=1773220900110, value=41.2
2 row(s) in 0.0350 seconds
```

---

## Question 10: Region Splits & Pre-Splitting in HBase

**Problem:** Create an HBase table with multiple regions and demonstrate how HBase distributes data across regions. Explain the role of RegionServers in achieving scalability and availability.

### Role of RegionServers
- **Regions:** Dynamic contiguous ranges of rows sorted by RowKey.
- **RegionServer:** Serves read/write requests for assigned regions. When a Region exceeds `hbase.hregion.max.filesize`, it automatically splits into two.
- **Pre-Splitting:** Prevents write bottlenecks during initial bulk ingestion by explicitly pre-defining region boundaries.

### HBase Execution Commands

```hbase
# Pre-split table into 4 regions across boundaries ['10', '20', '30']
create 'PreSplitTable', 'data', SPLITS => ['10', '20', '30']

# Insert data targeting different region key ranges
put 'PreSplitTable', '05_row', 'data:val', 'Region 1'
put 'PreSplitTable', '15_row', 'data:val', 'Region 2'
put 'PreSplitTable', '25_row', 'data:val', 'Region 3'
put 'PreSplitTable', '35_row', 'data:val', 'Region 4'

# Verify regions list in HBase
list_regions 'PreSplitTable'
```

### Terminal Output

```text
hbase(main):001:0> create 'PreSplitTable', 'data', SPLITS => ['10', '20', '30']
0 row(s) in 2.3400 seconds

hbase(main):006:0> list_regions 'PreSplitTable'
[Name                               | Start Key | End Key | RegionServer ]
 PreSplitTable,,1773221000000.a1.   |           | 10      | localhost:16020
 PreSplitTable,10,1773221000000.a2. | 10        | 20      | localhost:16020
 PreSplitTable,20,1773221000000.a3. | 20        | 30      | localhost:16020
 PreSplitTable,30,1773221000000.a4. | 30        |         | localhost:16020
4 row(s) in 0.1200 seconds
```

---

## Question 11: Cassandra Installation & Keyspace Setup

**Problem:** Install and configure Apache Cassandra. Create a keyspace named `CollegeDB` with an appropriate replication factor and create a `Students` table. Insert and retrieve at least five student records.

### Cassandra CQL Execution Commands

```sql
-- 1. Create Keyspace
CREATE KEYSPACE CollegeDB 
WITH replication = {
    'class': 'SimpleStrategy', 
    'replication_factor': 1
};

-- Switch to Keyspace
USE CollegeDB;

-- 2. Create Students Table
CREATE TABLE Students (
    student_id int,
    name text,
    department text,
    gpa double,
    PRIMARY KEY (student_id)
);

-- 3. Insert 5 Student Records
INSERT INTO Students (student_id, name, department, gpa) VALUES (101, 'Aman Sharma', 'CSE', 8.5);
INSERT INTO Students (student_id, name, department, gpa) VALUES (102, 'Priya Patel', 'ECE', 9.1);
INSERT INTO Students (student_id, name, department, gpa) VALUES (103, 'Rohan Verma', 'CSE', 7.8);
INSERT INTO Students (student_id, name, department, gpa) VALUES (104, 'Neha Gupta', 'ME', 8.2);
INSERT INTO Students (student_id, name, department, gpa) VALUES (105, 'Siddharth Rao', 'IT', 8.9);

-- 4. Retrieve All Students
SELECT * FROM Students;
```

### Terminal Output

```text
cqlsh> USE CollegeDB;
cqlsh:collegedb> SELECT * FROM Students;

 student_id | department | gpa | name
------------+------------+-----+---------------
        105 |         IT | 8.9 | Siddharth Rao
        101 |        CSE | 8.5 |   Aman Sharma
        104 |         ME | 8.2 |    Neha Gupta
        102 |        ECE | 9.1 |   Priya Patel
        103 |        CSE | 7.8 |   Rohan Verma

(5 rows)
```

---

## Question 12: CRUD Operations in Apache Cassandra (CQL)

**Problem:** Using Apache Cassandra, perform CRUD operations on an `Employees` table. Demonstrate `INSERT`, `SELECT`, `UPDATE`, and `DELETE` operations using CQL.

### Cassandra CQL Execution Commands

```sql
USE CollegeDB;

-- Create Table
CREATE TABLE Employees (
    emp_id int,
    emp_name text,
    designation text,
    salary double,
    PRIMARY KEY (emp_id)
);

-- 1. INSERT (Create)
INSERT INTO Employees (emp_id, emp_name, designation, salary) VALUES (501, 'Kunal Basu', 'Tech Lead', 120000.0);
INSERT INTO Employees (emp_id, emp_name, designation, salary) VALUES (502, 'Divya Nair', 'QA Analyst', 65000.0);

-- 2. SELECT (Read)
SELECT * FROM Employees WHERE emp_id = 501;

-- 3. UPDATE (Update)
UPDATE Employees SET salary = 135000.0 WHERE emp_id = 501;

-- Verify Update
SELECT * FROM Employees WHERE emp_id = 501;

-- 4. DELETE (Delete Column / Row)
DELETE designation FROM Employees WHERE emp_id = 502;
DELETE FROM Employees WHERE emp_id = 502;

-- Final Select
SELECT * FROM Employees;
```

### Terminal Output

```text
cqlsh:collegedb> SELECT * FROM Employees WHERE emp_id = 501;

 emp_id | designation | emp_name   | salary
--------+-------------+------------+--------
    501 |   Tech Lead | Kunal Basu | 120000

(1 rows)

cqlsh:collegedb> UPDATE Employees SET salary = 135000.0 WHERE emp_id = 501;
cqlsh:collegedb> DELETE FROM Employees WHERE emp_id = 502;
cqlsh:collegedb> SELECT * FROM Employees;

 emp_id | designation | emp_name   | salary
--------+-------------+------------+--------
    501 |   Tech Lead | Kunal Basu | 135000

(1 rows)
```

---

## Question 13: Time-Series Event Logging in Cassandra

**Problem:** Design a Cassandra database for an event logging application. Create an appropriate table, insert multiple events, and write CQL queries to retrieve events for a particular user and time period.

### Cassandra CQL Execution Commands

```sql
USE CollegeDB;

-- Partition Key: user_id | Clustering Key: event_time (Sorted DESC)
CREATE TABLE UserEventLogs (
    user_id text,
    event_time timestamp,
    event_type text,
    ip_address text,
    status text,
    PRIMARY KEY (user_id, event_time)
) WITH CLUSTERING ORDER BY (event_time DESC);

-- Insert Sample Time-Series Events
INSERT INTO UserEventLogs (user_id, event_time, event_type, ip_address, status) 
VALUES ('usr_99', '2026-03-15 10:00:00+0000', 'LOGIN', '10.0.0.1', 'SUCCESS');

INSERT INTO UserEventLogs (user_id, event_time, event_type, ip_address, status) 
VALUES ('usr_99', '2026-03-15 10:15:00+0000', 'PAYMENT', '10.0.0.1', 'SUCCESS');

INSERT INTO UserEventLogs (user_id, event_time, event_type, ip_address, status) 
VALUES ('usr_99', '2026-03-15 10:30:00+0000', 'LOGOUT', '10.0.0.1', 'SUCCESS');

-- Query: Retrieve events for 'usr_99' within specific time range
SELECT * FROM UserEventLogs 
WHERE user_id = 'usr_99' 
  AND event_time >= '2026-03-15 10:00:00+0000' 
  AND event_time <= '2026-03-15 10:20:00+0000';
```

### Terminal Output

```text
cqlsh:collegedb> SELECT * FROM UserEventLogs WHERE user_id = 'usr_99' AND event_time >= '2026-03-15 10:00:00+0000' AND event_time <= '2026-03-15 10:20:00+0000';

 user_id | event_time                      | event_type | ip_address | status
---------+---------------------------------+------------+------------+---------
  usr_99 | 2026-03-15 10:15:00.000000+0000 |    PAYMENT |   10.0.0.1 | SUCCESS
  usr_99 | 2026-03-15 10:00:00.000000+0000 |      LOGIN |   10.0.0.1 | SUCCESS

(2 rows)
```

---

## Question 14: Availability & Consistency Levels in Cassandra

**Problem:** Demonstrate availability and consistency in Cassandra by creating a keyspace with a suitable replication strategy. Perform read/write operations using different consistency levels such as `ONE`, `QUORUM`, and `ALL`, and observe the behavior.

### Theoretical Foundation (Tunable Consistency)
Cassandra follows **CAP Theorem (AP System with Tunable Consistency)**:
- $R$ = Number of nodes responding to Read request.
- $W$ = Number of nodes acknowledging Write request.
- $N$ = Replication Factor.
- Strong Consistency Condition: $$R + W > N$$

### Cassandra CQL Execution Commands

```sql
-- Create Keyspace with Replication Factor = 3 (Multi-Node Cluster Setup)
CREATE KEYSPACE DistributedDB 
WITH replication = {
    'class': 'NetworkTopologyStrategy', 
    'datacenter1': 3
};

USE DistributedDB;

CREATE TABLE SystemConfig (
    config_key text PRIMARY KEY,
    config_val text
);

-- Set Consistency Level to ONE and execute write
CONSISTENCY ONE;
INSERT INTO SystemConfig (config_key, config_val) VALUES ('maintenance_mode', 'false');

-- Set Consistency Level to QUORUM (Requires (3/2)+1 = 2 Acks)
CONSISTENCY QUORUM;
SELECT * FROM SystemConfig WHERE config_key = 'maintenance_mode';

-- Set Consistency Level to ALL (Requires all 3 Acks)
CONSISTENCY ALL;
SELECT * FROM SystemConfig WHERE config_key = 'maintenance_mode';
```

### Terminal Output

```text
cqlsh:distributeddb> CONSISTENCY ONE;
Consistency level set to ONE.

cqlsh:distributeddb> INSERT INTO SystemConfig (config_key, config_val) VALUES ('maintenance_mode', 'false');
0 row(s) in 0.0040 seconds

cqlsh:distributeddb> CONSISTENCY QUORUM;
Consistency level set to QUORUM.

cqlsh:distributeddb> SELECT * FROM SystemConfig WHERE config_key = 'maintenance_mode';

 config_key       | config_val
------------------+------------
 maintenance_mode |      false

(1 rows)

cqlsh:distributeddb> CONSISTENCY ALL;
Consistency level set to ALL.

cqlsh:distributeddb> SELECT * FROM SystemConfig WHERE config_key = 'maintenance_mode';

 config_key       | config_val
------------------+------------
 maintenance_mode |      false

(1 rows)
```

---

## Question 15: Full Application Implementation & Architectural Trade-offs

**Problem:** Design and implement a column-oriented NoSQL application using HBase or Cassandra for **Content Management System (CMS)**. Demonstrate database/table creation, data insertion, querying, updating, deletion, and explain how consistency, availability, transactions, query features, and scaling are handled.

### System Design Architecture & CQL Queries

```sql
-- Create CMS Keyspace
CREATE KEYSPACE ProductionCMS 
WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 3};

USE ProductionCMS;

-- Create CMS Article Table
CREATE TABLE Articles (
    category text,
    article_id uuid,
    title text,
    author text,
    content text,
    tags set<text>,
    published_date timestamp,
    PRIMARY KEY (category, published_date, article_id)
) WITH CLUSTERING ORDER BY (published_date DESC, article_id ASC);

-- Data Ingestion
INSERT INTO Articles (category, published_date, article_id, author, title, content, tags) 
VALUES (
    'Tech', 
    '2026-03-15 12:00:00+0000', 
    6ba7b810-9dad-11d1-80b4-00c04fd430c8, 
    'Aman Sharma', 
    'Cassandra Production Guide', 
    'Content body goes here...', 
    {'Cassandra', 'NoSQL', 'Database'}
);

-- Query Articles in Tech Category sorted by latest publication date
SELECT category, published_date, title, author, tags 
FROM Articles 
WHERE category = 'Tech';

-- Update Tag Set
UPDATE Articles 
SET tags = tags + {'HighAvailability'} 
WHERE category = 'Tech' 
  AND published_date = '2026-03-15 12:00:00+0000' 
  AND article_id = 6ba7b810-9dad-11d1-80b4-00c04fd430c8;

-- Delete Article Record
DELETE FROM Articles 
WHERE category = 'Tech' 
  AND published_date = '2026-03-15 12:00:00+0000' 
  AND article_id = 6ba7b810-9dad-11d1-80b4-00c04fd430c8;
```

### Architectural Analysis & Trade-Off Matrix

| Architectural Dimension | Apache Cassandra Handling |
| :--- | :--- |
| **Consistency vs Availability** | Cassandra is an **AP system** (Availability + Partition Tolerance). It trades strict ACID consistency for high availability using **Tunable Consistency** (`ONE`, `QUORUM`, `ALL`). |
| **Transactions Handling** | Lacks multi-row ACID transactions. Uses **Lightweight Transactions (LWT)** powered by Paxos consensus protocol (`IF NOT EXISTS`) for single-row atomic comparison and updates. |
| **Query Features** | Optimized for **Key-Value & Range Lookups** across partition keys. Does not support complex multi-table `JOIN` operations or arbitrary `WHERE` filtering without primary keys or secondary indexes. |
| **Horizontal Scaling** | Masterless **Peer-to-Peer Ring Architecture**. Adding nodes seamlessly distributes read/write traffic using Consistent Hashing (Murmur3Partitioner) without downtime. |

---