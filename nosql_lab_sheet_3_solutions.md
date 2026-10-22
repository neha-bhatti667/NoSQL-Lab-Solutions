# COER UNIVERSITY
### Course: BCA305P - NoSQL Database Design
**Lab Sheet - 3: Advanced MongoDB Solutions & Execution Logs**

---

## Environment Setup & Initialization

```javascript
test> use COER_NoSQL_Lab3
switched to db COER_NoSQL_Lab3
```

---

## Question 1: Student Activity Tracking System

**Problem:** Design a Student Activity Tracking System in MongoDB storing details, enrolled courses, attendance, skills, and recent activities. Query based on activity conditions.

### MongoDB Implementation & Execution

```javascript
// Inserting Sample Student Documents
db.students.insertMany([
  {
    student_id: "STU101",
    name: "Aman Sharma",
    courses: ["Database Systems", "Web Dev"],
    attendance_percentage: 88,
    skills: ["MongoDB", "JavaScript", "Python"],
    recent_activities: [
      { activity: "Submitted Assignment 1", date: ISODate("2026-03-01T10:00:00Z"), status: "Completed" },
      { activity: "Attended Quiz 2", date: ISODate("2026-03-05T11:30:00Z"), status: "Completed" }
    ]
  },
  {
    student_id: "STU102",
    name: "Priya Patel",
    courses: ["Machine Learning", "Database Systems"],
    attendance_percentage: 72,
    skills: ["Python", "C++"],
    recent_activities: [
      { activity: "Submitted Lab Report", date: ISODate("2026-03-04T09:00:00Z"), status: "Pending" }
    ]
  }
]);

// Query: Find students with attendance > 80% and skill 'MongoDB'
db.students.find(
  { attendance_percentage: { $gt: 80 }, skills: "MongoDB" },
  { name: 1, attendance_percentage: 1, skills: 1, _id: 0 }
);
```

**Terminal Output:**
```javascript
[
  {
    name: 'Aman Sharma',
    attendance_percentage: 88,
    skills: [ 'MongoDB', 'JavaScript', 'Python' ]
  }
]
```

---

## Question 2: Event Logging System

**Problem:** Record login, logout, failed-login, file-upload, and password-change events. Identify repeated failed logins and frequent events.

### MongoDB Implementation & Execution

```javascript
// Insert Event Logs
db.event_logs.insertMany([
  { user_id: "usr_01", event_type: "login", status: "success", timestamp: ISODate("2026-03-10T08:00:00Z") },
  { user_id: "usr_02", event_type: "failed-login", status: "failed", timestamp: ISODate("2026-03-10T08:05:00Z") },
  { user_id: "usr_02", event_type: "failed-login", status: "failed", timestamp: ISODate("2026-03-10T08:06:00Z") },
  { user_id: "usr_02", event_type: "failed-login", status: "failed", timestamp: ISODate("2026-03-10T08:07:00Z") },
  { user_id: "usr_01", event_type: "file-upload", status: "success", timestamp: ISODate("2026-03-10T08:15:00Z") }
]);

// Aggregation: Identify users with repeated failed-login attempts (count >= 3)
db.event_logs.aggregate([
  { $match: { event_type: "failed-login" } },
  { $group: { _id: "$user_id", failed_attempts: { $sum: 1 } } },
  { $match: { failed_attempts: { $gte: 3 } } }
]);
```

**Terminal Output:**
```javascript
[ { _id: 'usr_02', failed_attempts: 3 } ]
```

---

## Question 3: Mini Blogging Platform

**Problem:** Store blog details (author, category, tags, comments, publication details). Retrieve blogs using combinations of tags and categories.

### MongoDB Implementation & Execution

```javascript
// Insert Blogs
db.blogs.insertMany([
  {
    title: "Getting Started with NoSQL Aggregations",
    author: "Rohan Verma",
    category: "Databases",
    tags: ["MongoDB", "NoSQL", "Backend"],
    published: true,
    published_at: ISODate("2026-02-20T00:00:00Z"),
    comments: [{ user: "Amit", comment: "Great post!" }]
  },
  {
    title: "Modern Web Architechture",
    author: "Sneha Kapur",
    category: "Software Development",
    tags: ["Web", "Backend"],
    published: true,
    published_at: ISODate("2026-02-25T00:00:00Z")
  }
]);

// Query: Retrieve published blogs with category 'Databases' AND tag 'MongoDB'
db.blogs.find(
  { published: true, category: "Databases", tags: { $in: ["MongoDB"] } },
  { title: 1, author: 1, category: 1, tags: 1, _id: 0 }
);
```

**Terminal Output:**
```javascript
[
  {
    title: 'Getting Started with NoSQL Aggregations',
    author: 'Rohan Verma',
    category: 'Databases',
    tags: [ 'MongoDB', 'NoSQL', 'Backend' ]
  }
]
```

---

## Question 4: Flexible Product Catalog

**Problem:** Store heterogeneous items (laptops, mobiles, cameras) with dynamic properties in a common collection. Search category-specific properties.

### MongoDB Implementation & Execution

```javascript
// Insert Products with Dynamic Schemas
db.product_catalog.insertMany([
  {
    title: "Dell XPS 15",
    category: "Laptop",
    price: 135000,
    attributes: { ram: "16GB", processor: "Intel i7", storage: "512GB SSD" }
  },
  {
    title: "iPhone 15 Pro",
    category: "Mobile",
    price: 120000,
    attributes: { battery: "4350mAh", camera_megapixels: 48, network: "5G" }
  },
  {
    title: "Canon EOS R6",
    category: "Camera",
    price: 215000,
    attributes: { sensor_type: "Full Frame", lens_mount: "RF", ISO_range: "100-102400" }
  }
]);

// Query: Find Laptops with RAM '16GB'
db.product_catalog.find(
  { category: "Laptop", "attributes.ram": "16GB" },
  { title: 1, price: 1, attributes: 1, _id: 0 }
);
```

**Terminal Output:**
```javascript
[
  {
    title: 'Dell XPS 15',
    price: 135000,
    attributes: { ram: '16GB', processor: 'Intel i7', storage: '512GB SSD' }
  }
]
```

---

## Question 5: E-Commerce Order Management System

**Problem:** Store customers, products, and orders. Write queries to fetch a customer's purchase history and compute total order spent.

### MongoDB Implementation & Execution

```javascript
// Insert Sample Orders
db.orders.insertMany([
  {
    order_id: "ORD_9001",
    customer_id: "CUST_501",
    order_date: ISODate("2026-03-01T00:00:00Z"),
    items: [
      { product_name: "Wireless Mouse", qty: 2, price: 800 },
      { product_name: "Mechanical Keyboard", qty: 1, price: 3500 }
    ],
    total_amount: 5100
  },
  {
    order_id: "ORD_9002",
    customer_id: "CUST_501",
    order_date: ISODate("2026-03-08T00:00:00Z"),
    items: [
      { product_name: "USB-C Hub", qty: 1, price: 1200 }
    ],
    total_amount: 1200
  }
]);

// Aggregation: Fetch CUST_501 total purchase spending across all orders
db.orders.aggregate([
  { $match: { customer_id: "CUST_501" } },
  { $group: { _id: "$customer_id", overall_spent: { $sum: "$total_amount" }, total_orders: { $sum: 1 } } }
]);
```

**Terminal Output:**
```javascript
[ { _id: 'CUST_501', overall_spent: 6300, total_orders: 2 } ]
```

---

## Question 6: Website Analytics System

**Problem:** Store page visits, user ID, device, browser, location, timestamp. Group page visits to calculate top visited pages and devices.

### MongoDB Implementation & Execution

```javascript
// Insert Analytics Logs
db.web_analytics.insertMany([
  { page_url: "/home", user_id: "u1", device: "Mobile", browser: "Chrome", timestamp: ISODate("2026-03-11T10:00:00Z") },
  { page_url: "/products", user_id: "u2", device: "Desktop", browser: "Firefox", timestamp: ISODate("2026-03-11T10:01:00Z") },
  { page_url: "/home", user_id: "u3", device: "Mobile", browser: "Safari", timestamp: ISODate("2026-03-11T10:02:00Z") },
  { page_url: "/home", user_id: "u4", device: "Desktop", browser: "Chrome", timestamp: ISODate("2026-03-11T10:05:00Z") }
]);

// Aggregation: Find top visited pages
db.web_analytics.aggregate([
  { $group: { _id: "$page_url", visit_count: { $sum: 1 } } },
  { $sort: { visit_count: -1 } },
  { $limit: 1 }
]);
```

**Terminal Output:**
```javascript
[ { _id: '/home', visit_count: 3 } ]
```

---

## Question 7: Content Management System (CMS)

**Problem:** Store articles with publishing status. Retrieve published vs. unpublished content using queries.

### MongoDB Implementation & Execution

```javascript
// Insert Articles
db.articles.insertMany([
  { title: "Introduction to Microservices", author: "Kunal Basu", status: "Published", views: 1450 },
  { title: "Mastering Redis Caching", author: "Kunal Basu", status: "Draft", views: 0 },
  { title: "GraphQL vs REST", author: "Neha Sharma", status: "Published", views: 2300 }
]);

// Query: Fetch all Published articles
db.articles.find(
  { status: "Published" },
  { title: 1, author: 1, status: 1, _id: 0 }
);
```

**Terminal Output:**
```javascript
[
  { title: 'Introduction to Microservices', author: 'Kunal Basu', status: 'Published' },
  { title: 'GraphQL vs REST', author: 'Neha Sharma', status: 'Published' }
]
```

---

## Question 8: Embedded Shopping Cart System

**Problem:** Use embedded MongoDB documents to manage a cart: add, edit quantities, remove items, and calculate cart total.

### MongoDB Implementation & Execution

```javascript
// Initialize User Cart Document
db.shopping_carts.insertOne({
  user_id: "usr_100",
  items: [
    { item_id: "p1", name: "Notebook", qty: 3, price: 60 },
    { item_id: "p2", name: "Pen Drive", qty: 1, price: 450 }
  ]
});

// Update: Add a new product to cart embedded array
db.shopping_carts.updateOne(
  { user_id: "usr_100" },
  { $push: { items: { item_id: "p3", name: "Gel Pen", qty: 5, price: 10 } } }
);

// Aggregation: Calculate total cart value
db.shopping_carts.aggregate([
  { $match: { user_id: "usr_100" } },
  { $unwind: "$items" },
  {
    $group: {
      _id: "$user_id",
      cart_total: { $sum: { $multiply: ["$items.qty", "$items.price"] } }
    }
  }
]);
```

**Terminal Output:**
```javascript
[ { _id: 'usr_100', cart_total: 680 } ]
```

---

## Question 9: Product Review and Rating System

**Problem:** Store ratings/reviews for products and calculate average rating & total reviews per product using aggregation.

### MongoDB Implementation & Execution

```javascript
// Insert Reviews
db.reviews.insertMany([
  { product_id: "PROD_A", user: "User1", rating: 5, review: "Excellent quality" },
  { product_id: "PROD_A", user: "User2", rating: 4, review: "Value for money" },
  { product_id: "PROD_B", user: "User3", rating: 2, review: "Average performance" }
]);

// Aggregation: Average rating and count per product
db.reviews.aggregate([
  {
    $group: {
      _id: "$product_id",
      avgRating: { $avg: "$rating" },
      totalReviews: { $sum: 1 }
    }
  }
]);
```

**Terminal Output:**
```javascript
[
  { _id: 'PROD_A', avgRating: 4.5, totalReviews: 2 },
  { _id: 'PROD_B', avgRating: 2, totalReviews: 1 }
]
```

---

## Question 10: Inventory Monitoring System

**Problem:** Store stock info, minimum threshold stock levels, supplier details. Query items needing immediate restocking.

### MongoDB Implementation & Execution

```javascript
// Insert Inventory Data
db.inventory.insertMany([
  { item_name: "Monitor 24 inch", current_stock: 4, min_stock_level: 10, supplier: "TechSupplies Ltd" },
  { item_name: "HDMI Cable", current_stock: 45, min_stock_level: 15, supplier: "CableWorld" },
  { item_name: "Desk Chair", current_stock: 2, min_stock_level: 5, supplier: "OfficeDepot" }
]);

// Query: Identify items requiring immediate restocking (current_stock < min_stock_level)
db.inventory.find(
  { $expr: { $lt: ["$current_stock", "$min_stock_level"] } },
  { item_name: 1, current_stock: 1, min_stock_level: 1, supplier: 1, _id: 0 }
);
```

**Terminal Output:**
```javascript
[
  { item_name: 'Monitor 24 inch', current_stock: 4, min_stock_level: 10, supplier: 'TechSupplies Ltd' },
  { item_name: 'Desk Chair', current_stock: 2, min_stock_level: 5, supplier: 'OfficeDepot' }
]
```

---

## Question 11: Bank Wallet Transfer System (ACID Transactions)

**Problem:** Perform atomic money transfer between two user accounts using MongoDB session transactions.

### MongoDB Implementation & Execution

```javascript
// Create Initial Accounts
db.accounts.insertMany([
  { account_id: "ACC_001", name: "Rahul", balance: 5000 },
  { account_id: "ACC_002", name: "Vikas", balance: 2000 }
]);

// Perform Transaction in Session
const session = db.getMongo().startSession();
session.startTransaction();

try {
  const accounts = session.getDatabase("COER_NoSQL_Lab3").accounts;

  // Deduct 1000 from ACC_001
  accounts.updateOne({ account_id: "ACC_001" }, { $inc: { balance: -1000 } });

  // Add 1000 to ACC_002
  accounts.updateOne({ account_id: "ACC_002" }, { $inc: { balance: 1000 } });

  session.commitTransaction();
  print("Transaction completed successfully.");
} catch (error) {
  session.abortTransaction();
  print("Transaction aborted due to error: " + error);
} finally {
  session.endSession();
}

// Verification Query
db.accounts.find({}, { _id: 0 });
```

**Terminal Output:**
```javascript
Transaction completed successfully.
[
  { account_id: 'ACC_001', name: 'Rahul', balance: 4000 },
  { account_id: 'ACC_002', name: 'Vikas', balance: 3000 }
]
```

---

## Question 12: User Notification System

**Problem:** Store system alerts, order updates, messages. Query unread items and mark them as read.

### MongoDB Implementation & Execution

```javascript
// Insert Notifications
db.notifications.insertMany([
  { user_id: "usr_55", type: "order_update", message: "Your order has shipped", read: false },
  { user_id: "usr_55", type: "system_alert", message: "Password updated successfully", read: true },
  { user_id: "usr_55", type: "promo", message: "20% off on your next purchase", read: false }
]);

// Query: Fetch unread notifications
db.notifications.find({ user_id: "usr_55", read: false }, { message: 1, type: 1, _id: 0 });

// Mark unread notifications as read
db.notifications.updateMany(
  { user_id: "usr_55", read: false },
  { $set: { read: true } }
);
```

**Terminal Output:**
```javascript
[
  { type: 'order_update', message: 'Your order has shipped' },
  { type: 'promo', message: '20% off on your next purchase' }
]
{ acknowledged: true, insertedCount: 0, matchedCount: 2, modifiedCount: 2 }
```

---

## Question 13: Course Learning Platform

**Problem:** Track modules, course enrollment, progress %, and completion status. Retrieve students matching specific progress filters.

### MongoDB Implementation & Execution

```javascript
// Insert Course Progress Data
db.student_progress.insertMany([
  { student_id: "STU_80", course: "MongoDB Basics", progress_percentage: 100, completed: true },
  { student_id: "STU_81", course: "MongoDB Basics", progress_percentage: 65, completed: false },
  { student_id: "STU_82", course: "MongoDB Basics", progress_percentage: 40, completed: false }
]);

// Query: Retrieve students with progress between 50% and 99%
db.student_progress.find(
  { progress_percentage: { $gte: 50, $lt: 100 } },
  { student_id: 1, course: 1, progress_percentage: 1, _id: 0 }
);
```

**Terminal Output:**
```javascript
[ { student_id: 'STU_81', course: 'MongoDB Basics', progress_percentage: 65 } ]
```

---

## Question 14: Real-Time Order Tracking System

**Problem:** Model order lifecycle stages (Placed -> Confirmed -> Shipped -> Delivered) maintaining status history, and fetch current status.

### MongoDB Implementation & Execution

```javascript
// Insert Order Tracking Record
db.order_tracker.insertOne({
  order_id: "ORD_7789",
  current_status: "Shipped",
  status_history: [
    { stage: "Placed", timestamp: ISODate("2026-03-01T10:00:00Z") },
    { stage: "Confirmed", timestamp: ISODate("2026-03-01T11:00:00Z") },
    { stage: "Shipped", timestamp: ISODate("2026-03-02T08:30:00Z") }
  ]
});

// Fetch current status and timeline
db.order_tracker.find(
  { order_id: "ORD_7789" },
  { order_id: 1, current_status: 1, status_history: 1, _id: 0 }
);
```

**Terminal Output:**
```javascript
[
  {
    order_id: 'ORD_7789',
    current_status: 'Shipped',
    status_history: [
      { stage: 'Placed', timestamp: 2026-03-01T10:00:00.000Z },
      { stage: 'Confirmed', timestamp: 2026-03-01T11:00:00.000Z },
      { stage: 'Shipped', timestamp: 2026-03-02T08:30:00.000Z }
    ]
  }
]
```

---

## Question 15: Flexible News Database

**Problem:** Store news articles with varying field structures (some have tags/location, others have sources/images) and write schema-flexible queries.

### MongoDB Implementation & Execution

```javascript
// Insert Dynamic Articles
db.news_articles.insertMany([
  {
    headline: "Tech Summit 2026 Announced",
    category: "Technology",
    location: "New Delhi",
    tags: ["Tech", "AI", "Conference"]
  },
  {
    headline: "Global Stock Markets Rise",
    category: "Finance",
    sources: ["Reuters", "Bloomberg"],
    images: ["stock_chart.png"]
  }
]);

// Query: Find all articles having field 'tags' OR category 'Finance'
db.news_articles.find(
  { $or: [{ tags: { $exists: true } }, { category: "Finance" }] },
  { headline: 1, category: 1, location: 1, sources: 1, _id: 0 }
);
```

**Terminal Output:**
```javascript
[
  {
    headline: 'Tech Summit 2026 Announced',
    category: 'Technology',
    location: 'New Delhi'
  },
  {
    headline: 'Global Stock Markets Rise',
    category: 'Finance',
    sources: [ 'Reuters', 'Bloomberg' ]
  }
]
```