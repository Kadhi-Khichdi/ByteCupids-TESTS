Provided Problem statements from claude in last iteration along with this prompt
 
## PROMPT

You are a senior AI educator tasked with merging multiple raw problem statements into one or more **progressively structured, interconnected problem sets** that thoroughly teach the given topic.


### 🔧 Objective:

Given 3–4 separate problem statements, your job is to:
- Combine them into a **single, rich, multi-part challenge**, or
- Design a **sequence of 2–3 learning arcs** that together help a learner deeply grasp the topic
- Smoothly integrate increasing difficulty levels
- Add transitions, context, and framing to make it feel **cohesive and intentional**
- Maintain ambiguity where needed — avoid giving answers
- Ensure it feels like a *narrative*, not a test

---

### 🧾 Input Metadata:

- **Topic:** {{topic_name}}
- **Module:** {{module_name}}
- **Audience:** Self-guided learners with chat/voice tutor agent
- **Use Case:** These problem arcs will be used by a downstream discussion agent to walk learners through discovery and solution-building

---

### 🧩 Raw Problems:

1. [Easy] {{problem_1}}
2. [Medium] {{problem_2}}
3. [Hard] {{problem_3}}
4. [Real-World] {{problem_4}}

---

### ✅ Output Format:

```markdown
## Composite Problem Set: {{topic_name}}

### Problem Arc 1: [Foundations of {{topic}}]
(Combine Easy + Medium)

...

### Problem Arc 2: [Deep Dive & Edge Cases]
(Use Hard challenge)

...

### Problem Arc 3: [Real-World Simulation]
(Frame Real-World scenario with enough narrative and open ends)

...
```


## Composite Problem Set: CRUD in MySQL

This problem set guides you through the fundamental Create, Read, Update, and Delete (CRUD) operations in MySQL, starting with basic syntax and progressing to complex, real-world scenarios involving transactions, performance optimization, and schema design.

### Problem Arc 1: Core CRUD Operations and Data Integrity

**Objective:** Master the basic SQL commands for CRUD (`INSERT`, `SELECT`, `UPDATE`, `DELETE`) and understand how constraints and transactions maintain data integrity in related tables.

**Scenario:** You'll start by managing a simple product inventory and then move to a slightly more complex library database.

**Part 1: Managing a Product Inventory**

Imagine you're setting up a small online store. Your first task is to manage the `products` table. It has the following structure:

*   `id` (INT, AUTO_INCREMENT, PRIMARY KEY)
*   `name` (VARCHAR(255))
*   `price` (DECIMAL(10, 2))
*   `stock_count` (INT)
*   `category` (VARCHAR(255))

1.  **Create:** A new shipment arrived! Write the MySQL statement to add a "Wireless Mouse" with a price of $29.99, 45 units in stock, under the "Peripherals" category. Which SQL command performs this 'Create' operation?
2.  **Read:** You need to see all items in a specific category. Write the MySQL statement to retrieve all products listed under "Peripherals". Which SQL command performs this 'Read' operation?
3.  **Update:** The price for the mouse needs adjustment. Write the MySQL statement to change the price of the "Wireless Mouse" to $24.99. Which SQL command performs this 'Update' operation? What's crucial to remember when writing `UPDATE` statements to avoid unintended changes?
4.  **Delete:** Time to clear out old stock. Write the MySQL statement to remove all products with a `stock_count` of 0. Which SQL command performs this 'Delete' operation? What safety precautions should you consider before running such a `DELETE` statement?

**Part 2: Building a Library Database**

Your next task involves managing a library's `books` and `authors` tables. Notice the relationship between them via `author_id`.

**`authors` Table:**

*   `id` (INT, PRIMARY KEY, AUTO_INCREMENT)
*   `name` (VARCHAR(255))
*   `nationality` (VARCHAR(255))

**`books` Table:**

*   `id` (INT, PRIMARY KEY, AUTO_INCREMENT)
*   `title` (VARCHAR(255))
*   `author_id` (INT, FOREIGN KEY referencing `authors.id`)
*   `isbn` (VARCHAR(20), UNIQUE)
*   `published_date` (DATE)
*   `available` (BOOLEAN)

1.  **Create (with Transaction & Foreign Keys):** A new author joins the library's collection! Write the necessary MySQL statements to:
    *   Insert a new author (e.g., "George Orwell", "British").
    *   Immediately after, insert two books by this author (e.g., "1984", "Animal Farm").
    *   How can you get the `id` of the newly inserted author to use as the `author_id` for the books?
    *   Why should these operations be wrapped in a transaction (`START TRANSACTION; ... COMMIT;`)? What happens if the second book insertion fails due to a duplicate ISBN, but the author and the first book were already inserted?
2.  **Read (with Joins):** A patron wants books by British authors. Write a MySQL statement using a `JOIN` to retrieve the `title` of all *available* books along with their author's `name` for authors with the nationality "British".
3.  **Update:** The library is archiving older books. Write a MySQL statement to set the `available` status to `FALSE` for all books published before '2010-01-01'.
4.  **Delete (Conditional & Referential Integrity):** You need to remove an author, but only if they have no books listed. How would you approach writing a `DELETE` statement for an author (e.g., by `id`) that *only* succeeds if no books reference that author in the `books` table? What database mechanism (related to Foreign Keys) typically prevents deleting an author who still has books associated with them?

Think about potential constraint violations (UNIQUE ISBN, Foreign Key `author_id`) in these steps. How does MySQL react to these violations during `INSERT` or `UPDATE`?

### Problem Arc 2: Advanced CRUD, Transactions, and Performance Optimization

**Objective:** Apply CRUD operations to more complex scenarios involving multiple related tables, implement robust transactions, optimize queries for performance, and understand advanced techniques like soft deletes.

**Scenario:** You are now working with a more complex e-commerce database involving customers, orders, and order items. Performance and data consistency are critical.

**`customers` Table:**

*   `id` (INT, PRIMARY KEY, AUTO_INCREMENT)
*   `name` (VARCHAR(255))
*   `email` (VARCHAR(255), UNIQUE)
*   `created_at` (TIMESTAMP DEFAULT CURRENT_TIMESTAMP)
*   `status` (ENUM('active', 'inactive', 'suspended'))

**`orders` Table:**

*   `id` (INT, PRIMARY KEY, AUTO_INCREMENT)
*   `customer_id` (INT, FOREIGN KEY referencing `customers.id`)
*   `order_date` (TIMESTAMP DEFAULT CURRENT_TIMESTAMP)
*   `total_amount` (DECIMAL(10, 2))
*   `status` (ENUM('pending', 'shipped', 'delivered', 'cancelled'))

**`order_items` Table:**

*   `id` (INT, PRIMARY KEY, AUTO_INCREMENT)
*   `order_id` (INT, FOREIGN KEY referencing `orders.id`)
*   `product_id` (INT) -- Assume FK to a products table
*   `quantity` (INT)
*   `unit_price` (DECIMAL(10, 2))

1.  **Complex Transaction: Order Cancellation**
    Design the sequence of MySQL statements required to implement an "order cancellation" feature. This process must:
    *   First, check if the target order's `status` is 'pending'. Proceed only if it is.
    *   Update the order's `status` to 'cancelled'.
    *   Insert a record into an `audit_log` table (imagine columns like `timestamp`, `user_id`, `action`, `details`) noting the cancellation.
    *   Ensure this entire sequence is *atomic*. If any step fails (e.g., the audit log insert fails), the order status should not remain 'cancelled'.
    Provide the statements wrapped in a transaction. How would you handle the check for the 'pending' status within your SQL logic or application code?

2.  **Optimized Read: Top Customers Report**
    You need to generate a report showing the top 10 active customers based on their spending in the last 3 months. The report should show the customer's name, email, total number of orders (in that period), and total amount spent (in that period). Cancelled orders should be excluded.
    *   Write the `SELECT` query to generate this report. Consider using joins, aggregation (`SUM`, `COUNT`), filtering (`WHERE`), ordering (`ORDER BY`), and limiting (`LIMIT`).
    *   This query might be slow on a large database. What indexes would you create on the `customers`, `orders` tables to significantly speed it up? Explain *why* each index helps (e.g., for filtering, joining, ordering). How could you use `EXPLAIN` to verify the effectiveness of your indexes?

3.  **Soft Delete Strategy: Managing Customers**
    Instead of permanently deleting customers (which could orphan order records or lose valuable history), the business wants to implement a "soft delete" strategy.
    *   How would you modify the `customers` table schema to support soft deletes? (Consider adding columns like `deleted_at` TIMESTAMP NULLABLE).
    *   Show the `UPDATE` statement to soft-delete a customer (e.g., by setting `deleted_at` to the current time).
    *   How would you modify your standard `SELECT` queries (e.g., finding a customer by email) to only return *active* (not soft-deleted) customers?
    *   A key challenge: How do you ensure a *new* customer cannot register with the same email address as a *soft-deleted* customer, while still allowing the soft-deleted record to exist? (Hint: Think about unique constraints and how they interact with NULL values, or alternative approaches).

Consider potential race conditions. What might happen if two users try to update the same order or customer simultaneously? How do transactions and potentially optimistic locking (using a version column) help mitigate these issues?

### Problem Arc 3: Real-World Simulation: Social Media Platform CRUD

**Objective:** Design a database schema and implement core CRUD operations for a realistic application, considering scalability, security, performance, and best practices from the ground up.

**Scenario:** You are tasked with designing the database backend for a new social media platform. Users can post content, follow others, like/comment, and receive notifications.

1.  **Schema Design:**
    Design the necessary MySQL tables to support the core features. Define at least the following tables: `users`, `posts`, `follows` (many-to-many relationship between users), `comments`, `likes`, `notifications`.
    For each table, specify:
    *   Column names, appropriate data types (consider `TEXT` for posts/comments, `TIMESTAMP` for dates).
    *   Primary Keys (PK).
    *   Foreign Keys (FK) defining relationships (e.g., a comment belongs to a post and a user). Specify `ON DELETE` behavior where relevant (e.g., what happens to comments if a post is deleted?).
    *   Unique constraints (e.g., user email, preventing duplicate likes/follows).
    *   Essential indexes to support common queries (justify your choices).
    Provide the `CREATE TABLE` statements.

2.  **Core CRUD Implementation:**
    Write optimized MySQL statements (using placeholders like `?` or `:param` to indicate where application parameters would go, demonstrating SQL injection prevention) for these critical operations:
    *   **User Registration:** `INSERT` a new user, ensuring email uniqueness.
    *   **Create Post:** `INSERT` a new post linked to the user who created it.
    *   **Follow User:** `INSERT` a record into the `follows` table (user A follows user B). How do you prevent duplicate follow entries?
    *   **Like/Unlike Post:** Implement logic to add a 'like' record. If the user tries to like the same post again, nothing should happen (idempotency). Consider how to handle unliking (`DELETE`). A `UNIQUE` constraint on `(user_id, post_id)` in the `likes` table is crucial here.
    *   **Mark Notification Read:** `UPDATE` a notification's status (e.g., set `is_read` to true or `read_at` timestamp).

3.  **Advanced Queries & Real-World Concerns:**
    Address the implementation of these more complex features and concerns:
    *   **User Feed Query:** Design a `SELECT` query to retrieve the latest posts from users that the current user follows. This query needs to be efficient and paginated. Explain your pagination strategy (e.g., keyset/cursor-based vs. offset-based) and provide the paginated query structure. What indexes are critical for making this feed query fast?
    *   **Like/Comment Counts:** How would you efficiently retrieve the number of likes and comments for a given post? Would you store these counts directly on the `posts` table (denormalization) and update them using triggers/application logic, or calculate them on the fly with `COUNT(*)`? Discuss the trade-offs.
    *   **Transactions:** Identify operations from step 2 or 3 that *must* be performed within a transaction to ensure data consistency. Explain why.
    *   **Scalability:** What are potential database bottlenecks as the platform grows (e.g., feed generation for users following many people, like counts for viral posts)? Briefly suggest strategies to address these (e.g., caching, read replicas, denormalization, sharding).
    *   **Soft Deletes:** Where might soft deletes be appropriate in this application (e.g., `users`, `posts`, `comments`)? Justify your choices.

This final arc requires you to synthesize your understanding of CRUD, schema design, transactions, indexing, and operational considerations to build a robust and scalable system foundation. Explain your reasoning and design trade-offs clearly.

## CLAUDE :-

# Composite Problem Set: CRUD in MySQL

## Problem Arc 1: Building the Digital Inventory System

*You've recently joined Northside Electronics as their new database administrator. The small but growing retail business needs to modernize how they track their inventory and manage product information.*

### Background Context
The store manager has been using spreadsheets to track inventory, which has become increasingly difficult as the business grows. They've decided to implement a MySQL database for better data management. You've already set up a database with a `products` table that has the following structure:

```sql
CREATE TABLE products (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  price DECIMAL(10,2) NOT NULL,
  stock_count INT NOT NULL DEFAULT 0,
  category VARCHAR(50)
);
```

### Part A: Implementing Basic Inventory Management
The store manager has handed you a list of new products that just arrived and needs your help adding them to the database.

1. First, explore how you would add this new "Wireless Mouse" to the inventory system:
   - Product Name: Wireless Mouse
   - Price: $29.99
   - Stock Count: 45
   - Category: Peripherals

2. The manager now wants to see all products in the "Peripherals" category to compare the new mouse with existing inventory. How would you retrieve this information?

3. After market research, the manager decides that the Wireless Mouse should be priced at $24.99 instead. How would you update this specific product's price?

4. During inventory cleanup, you notice several products with 0 stock that haven't been available for months. How would you remove these items from the database?

### Part B: Managing the Growing Product Catalog
As the business expands, you need more sophisticated queries to manage inventory effectively.

1. The manager wants to know which product categories have the lowest average inventory levels. Design a query that shows each category and its average stock count, ordered from lowest to highest.

2. For a promotional email, you need to find all products priced between $20 and $50 with at least 10 items in stock. How would you structure this query?

3. The business needs to apply a 10% price increase to all products in the "Laptops" category that are priced under $500. Write the SQL statement that would accomplish this update.

### Reflection Point
- How do the four basic CRUD operations (Create, Read, Update, Delete) map to specific SQL commands you've used so far?
- What integrity concerns should you consider when designing operations that modify your product data?
- How might these operations need to change if multiple users were updating the database simultaneously?

## Problem Arc 2: The Library Management System

*The local community library has approached you after hearing about your successful work with Northside Electronics. They need help transitioning from their paper-based system to a digital database that tracks their books and authors.*

### Scenario Introduction
The library director shows you their current system: index cards for books and a separate author directory. You recognize the need for a relational database structure and have created two tables:

```sql
CREATE TABLE authors (
  id INT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  nationality VARCHAR(50)
);

CREATE TABLE books (
  id INT PRIMARY KEY,
  title VARCHAR(200) NOT NULL,
  author_id INT,
  isbn VARCHAR(20) UNIQUE,
  published_date DATE,
  available BOOLEAN DEFAULT TRUE,
  FOREIGN KEY (author_id) REFERENCES authors(id)
);
```

### Challenge Components

1. The library has acquired works by renowned author Gabriel García Márquez. Develop a transaction that:
   - Adds Márquez to the authors table (if not already present)
   - Inserts his famous works "One Hundred Years of Solitude" and "Love in the Time of Cholera" to the books table
   - Ensures that if any part of this process fails, no partial data is committed

2. The library is planning a cultural exhibition featuring books by authors from specific countries. Design a query that finds all available books written by authors of a particular nationality, displaying both the book title and author name in the results.

3. The library is undergoing renovation, and books published before 2010 will be temporarily unavailable. Create an update operation that:
   - Changes the availability status of all pre-2010 books to FALSE
   - Reports back how many books were affected by this change

4. The director is concerned about accidentally removing author records who still have books in circulation. Implement a safeguard that:
   - Prevents the deletion of authors who still have books in the database
   - Allows deletion only when an author has no associated books
   - Provides meaningful feedback when a deletion is attempted but prevented

### Critical Analysis
- What challenges arise when working with related tables compared to a single table?
- How do foreign key constraints protect data integrity, and what complications might they introduce?
- In what scenarios would transactions be critical for maintaining consistent library data?
- How might you modify these operations if you needed to track which staff member made each change?

## Problem Arc 3: Building a Social Media Platform

*Your database expertise has attracted the attention of a startup called ConnectHub that's building a new social media platform. They need your help designing the database architecture and implementing core functionality that can scale with their growth.*

### Situational Background
ConnectHub aims to create a platform where users can create profiles, share content, connect with others, and engage through likes and comments. As their database architect, you need to design a robust system that handles these interactions efficiently while planning for future growth.

The platform needs to support:
- User registration and profile management
- Content posting and sharing
- Social connections (follows/followers)
- Engagement metrics (likes, comments)
- Activity notifications
- Content feeds customized for each user

### Open-Ended Challenge

1. **Database Design Phase**:
   - Design the core tables needed for this social media platform
   - Consider what columns each table should have
   - Establish relationships between tables using primary and foreign keys
   - Determine which indexes would improve performance for common operations
   - Decide where constraints should be implemented to maintain data integrity

2. **Implementation Phase**:
   After designing your schema, implement SQL statements for these critical platform features:

   a) **User Management**:
   - Create a registration process that prevents duplicate email addresses
   - Implement profile updates while maintaining data history
   - Design a "soft delete" strategy that preserves user data for analytics while removing visibility

   b) **Social Connection Features**:
   - Implement the "follow" functionality between users
   - Create an efficient query to display all followers of a given user
   - Design a system to recommend new connections based on existing relationships

   c) **Content and Engagement**:
   - Develop the process for creating and displaying posts
   - Implement like/unlike functionality that prevents duplicates
   - Design an efficient method to count engagement metrics
   - Create a personalized feed query that shows posts from followed users, sorted by recency

   d) **Performance and Scaling**:
   - Implement proper pagination for feed queries
   - Design an efficient notification system
   - Identify potential database bottlenecks and how you would address them

### Extension Possibilities
- How would you modify your design to incorporate post categorization or hashtags?
- What changes would be needed to support direct messaging between users?
- How might you implement content moderation flags and processes?
- What privacy controls would you build into the database design?
- How would you approach data archiving for older content while maintaining reference integrity?

---

**Thinking Points Throughout:**
- How does each implementation demonstrate the four CRUD operations?
- What specific MySQL features help optimize these operations?
- How do constraints and transactions work together to maintain data integrity?
- What performance considerations become more important as the database grows?