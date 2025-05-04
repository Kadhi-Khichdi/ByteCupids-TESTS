Provided Problem statements from claude in last iteration along with this prompt

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
```