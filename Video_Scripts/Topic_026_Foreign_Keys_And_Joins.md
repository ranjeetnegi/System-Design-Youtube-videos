# What Are Foreign Keys and JOINs?

## Video Length: ~4-5 minutes | Level: Beginner

---

## The Hook (20-30 seconds)

A library. Books on shelves. Each book has a card inside: "Belongs to Shelf A-12." You don't store the whole shelf inside the book. You store a *reference*—a pointer. "A-12." When you need the shelf, you look it up. That's how databases connect tables. Not by copying everything. By storing a reference. That reference? A **foreign key**. And when you want to pull data from multiple tables at once? That's a **JOIN**. Let me show you why this is one of the most important ideas in databases.

---

## The Story

Imagine you have a **users** table. User ID 1 is Alice. User ID 2 is Bob. Now you have an **orders** table. Order 101: "2 shirts, $50." Who placed it? You could write "Bob" in every order. But Bob changes his name to "Robert." Now you have to update 47 orders. Messy. Instead, you store **user_id: 2**. A reference. "This order belongs to user 2." The **foreign key** is that reference—it points to the **primary key** of another table. **Referential integrity** means the database enforces it: you can't have an order for user_id 999 if user 999 doesn't exist. The database won't let you. Relationships stay clean.

**JOINs** are how you combine tables. You have orders with user_id. You want the user's name. You don't run two queries. You **JOIN** users and orders. "Give me orders with their user names." The database matches user_id in orders to id in users. One query. Combined result. **INNER JOIN** returns only rows where both tables have a match. Order 101 + User 2 (Bob) = one row. **LEFT JOIN** returns all rows from the left table, even if there's no match on the right. **RIGHT JOIN** does the opposite. Most of the time you use INNER or LEFT. Simple. Powerful.

What happens when you delete a user who has orders? **Cascade delete** = delete the user, delete their orders. **Set null** = delete the user, set user_id to null in orders (orphaned orders). **Restrict** = don't allow the delete if orders exist. You choose. The foreign key defines the relationship—and the rules.

---

## Another Way to See It

Think of a filing cabinet. Drawer 1: "Customer files." Each file has an ID. Drawer 2: "Invoice files." Each invoice says "Customer: 47." You don't photocopy the whole customer file into every invoice. You write "47." When you need the customer's address for invoice 47, you open both drawers. You match "Customer: 47" with "File 47" in the customer drawer. That matching? That's a JOIN. The "47" on the invoice? That's the foreign key.

---

## Connecting to Software

- **Foreign key** = a column that references the primary key of another table. Enforces relationships. Prevents orphaned data.
- **Referential integrity** = the database guarantees that every foreign key points to a real row. No user_id 999 if user 999 doesn't exist.
- **INNER JOIN** = only rows where both tables match. "Orders with their users"—no orders without users, no users without orders in the result.
- **LEFT JOIN** = all rows from the left table + matching rows from the right. Right side can be null if no match.
- **RIGHT JOIN** = all rows from the right table + matching rows from the left. Less common; often you can flip the tables and use LEFT.
- **Cascade / Set null / Restrict** = what happens on delete. Cascade deletes children. Set null clears the reference. Restrict blocks the delete.
- **Real tools:** PostgreSQL, MySQL, SQL Server—all support foreign keys and JOINs. ORMs like **SQLAlchemy**, **Django ORM**, **TypeORM** abstract this but generate JOINs under the hood.

---

## Let's Walk Through the Diagram

```
    USERS TABLE                    ORDERS TABLE
    ============                   =============
    id (PK) | name                 id (PK) | user_id (FK) | total
    -------|-------                -------|--------------|------
    1      | Alice                 101    | 2            | 50
    2      | Bob                   102    | 1            | 30
    3      | Carol                 103    | 2            | 75

    INNER JOIN: orders JOIN users ON orders.user_id = users.id
    Result: 101-Bob-50, 102-Alice-30, 103-Bob-75

    LEFT JOIN: orders LEFT JOIN users ON orders.user_id = users.id
    (Same here—all orders have users. If order 104 had user_id=99, 
     user columns would be NULL)

    Foreign key: orders.user_id → users.id
    "Every order must belong to a real user"
```

---

## Real-World Examples (2-3)

**1. E-commerce (users → orders → products).** Users table. Orders table with user_id (foreign key to users). Order_items table with order_id (FK to orders) and product_id (FK to products). One user, many orders. One order, many items. JOINs let you query "all orders for Alice with product names" in one shot. Without foreign keys, you could have order_items pointing to deleted orders. Chaos.

**2. Social media (users → posts → comments).** Users. Posts with user_id. Comments with post_id and user_id. "Show me all comments on this post with commenter names." JOIN posts, comments, users. Foreign keys ensure every comment belongs to a real post and a real user.

**3. Hospital records (patients → appointments → doctors).** Patients. Appointments with patient_id and doctor_id. Both are foreign keys. You can't schedule an appointment for a patient who doesn't exist. Referential integrity protects the data.

---

## Let's Think Together

**Question:** You have 10,000 orders. 50 users. You want "all orders with user names." Do you run 10,000 queries (one per order) or one query with a JOIN?

**Pause. Think about it...**

**Answer:** One query with a JOIN. The database does the matching in one pass. 10,000 separate queries would be 10,000 round-trips. Slow. Expensive. A JOIN is a single operation. The database is built for this. Always prefer JOINs over "query in a loop."

---

## What Could Go Wrong? (Mini Disaster Story)

A startup stored user_id in orders as a plain number. No foreign key. "We'll enforce it in the app." One bug: a script deleted a user. The app didn't check. 2,000 orders suddenly had user_id pointing to a ghost. Reports broke. "Revenue by user" showed null. Support couldn't find who placed orders. They had to restore from backup, fix the bug, add foreign keys, and migrate. Two weeks of pain. The fix? Foreign keys from day one. Let the database enforce the rules. Don't trust the application alone.

---

## Surprising Truth / Fun Fact

**JOINs** can get expensive. Three tables, each with millions of rows? A naive JOIN might compare every row to every row—billions of operations. Databases use **indexes** on foreign key columns to make JOINs fast. That's why creating a foreign key often automatically suggests an index. The relationship isn't just for integrity—it's for performance too.

---

## Quick Recap (5 bullets)

- **Foreign key** = a column that references another table's primary key. Enforces relationships.
- **Referential integrity** = the database guarantees every foreign key points to a real row.
- **INNER JOIN** = only matching rows. **LEFT JOIN** = all left rows + matches. **RIGHT JOIN** = all right rows + matches.
- **Cascade / Set null / Restrict** = define what happens when you delete a referenced row.
- **JOINs** combine tables in one query. Use them instead of multiple queries in a loop.

---

## One-Liner to Remember

> **A foreign key is a reference; a JOIN is the lookup. Together they connect your tables without chaos.**

---

## Next Video

You've got users, orders, and JOINs. Your database is structured. But what if someone puts malicious data into your queries? Not a typo—*malicious*. A single quote. A crafted string. And suddenly your database does something you never intended. That's **SQL injection**—and it's one of the most dangerous attacks on the web. That's next.
