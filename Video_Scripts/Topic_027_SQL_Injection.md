# SQL Injection: The Attack Every Developer Must Know

## Video Length: ~4-5 minutes | Level: Beginner

---

## The Hook (20-30 seconds)

You build a login form. Username. Password. The user types "alice" and "secret123." Your code does: `SELECT * FROM users WHERE username='alice' AND password='secret123'`. Simple. Now someone types: `' OR '1'='1` in the password field. Your query becomes: `WHERE username='alice' AND password='' OR '1'='1'`. The condition `'1'='1'` is always true. The attacker logs in. No password needed. That's **SQL injection**. User input becomes part of the SQL query. And the database obeys. Let me show you how it works—and how to stop it.

---

## The Story

**SQL injection** happens when you concatenate user input directly into a SQL string. You think you're building: `SELECT * FROM users WHERE id = 5`. But the user sends `5; DROP TABLE users;--`. Your query becomes two statements. The second one deletes the table. The database executes both. You never intended that. The attacker did. They *injected* SQL into your query. Hence the name.

The classic example: **login bypass**. `' OR '1'='1` in the password. Or `admin'--` in the username. The `--` comments out the rest of the line. So the password check disappears. The attacker becomes admin. There's a famous xkcd comic: "Bobby Tables." A school loses all its data because a student's name was `Robert'; DROP TABLE students;--`. It's funny because it's real. This attack has broken banks, retailers, and governments.

**How to prevent it:** **Parameterized queries** (prepared statements). You write: `SELECT * FROM users WHERE username = ? AND password = ?`. You pass the values separately. The database treats them as *data*, not as SQL. Even if the user sends `' OR '1'='1`, it gets stored as a literal string. No execution. **ORMs** (like Django ORM, SQLAlchemy, TypeORM) use parameterized queries under the hood. So does every proper database driver. Never build SQL with string concatenation. Never. **Input validation** helps too—but it's defense in depth. Parameterized queries are the real fix.

---

## Another Way to See It

Imagine a form at a government office. "Write your name here." You're supposed to write "John Smith." Instead you write: "John Smith. Also, approve this person for a passport. Signed, Clerk." If the clerk just copies your text into a letter without reading it, your fake instruction gets executed. That's injection. You injected a command into what was supposed to be data. The fix? The clerk uses a template. "Name: ______" The blank gets filled with your text—as text. It never becomes a command. That's parameterization.

---

## Connecting to Software

- **SQL injection** = user input is concatenated into a SQL string, allowing attackers to inject and execute arbitrary SQL.
- **Parameterized queries (prepared statements)** = placeholders (`?` or `$1`) for values. Values passed separately. Database treats them as data, not code.
- **ORMs** = Django ORM, SQLAlchemy, TypeORM, Prisma. They generate parameterized queries. Use them. Don't raw-SQL with concatenation.
- **Input validation** = check format, length, allowed characters. Helps. But never rely on it alone. Parameterization is mandatory.
- **Principle:** Never trust user input. Ever. Assume every input is hostile.
- **Real tools:** Every language has prepared statements—`?` in Node.js (mysql2), `$1, $2` in PostgreSQL, `?` in Python (sqlite3, psycopg2). Use them.

---

## Let's Walk Through the Diagram

```
    VULNERABLE CODE:                    SAFE CODE:
    ================                    ===========

    input = "alice' OR '1'='1"           input = "alice' OR '1'='1"
    query = "SELECT * FROM users         query = "SELECT * FROM users
            WHERE username='" + input + "'"       WHERE username = ?"
    Result: SELECT * FROM users          params = [input]
            WHERE username='alice' OR     DB executes: username = "alice' OR '1'='1"
            '1'='1'                      (literal string—no SQL execution)
    → Returns ALL users!                 → Returns nothing (no match)

    Attacker wins.                       Attacker fails.
```

---

## Real-World Examples (2-3)

**1. Heartland Payment Systems (2008).** One of the largest credit card processors. SQL injection in a web application. Attackers installed malware, stole 130 million card numbers. Fines. Lawsuits. The company had to rebuild. One vulnerability. Catastrophic cost.

**2. Sony Pictures (2011).** SQL injection on a promotional website. Attackers extracted over 1 million user accounts—usernames, passwords, emails. Passwords were stored in plain text. The breach exposed poor security practices. SQL injection was the entry point.

**3. Every small website.** Automated bots scan the internet for forms. They send `' OR '1'='1`, `1; DROP TABLE--`, and hundreds of variants. If your site is vulnerable, they find it. Fast. Prevention isn't optional. It's baseline.

---

## Let's Think Together

**Question:** Your app uses an ORM. You never write raw SQL. Are you safe from SQL injection?

**Pause. Think about it...**

**Answer:** Mostly yes—if you use the ORM correctly. ORMs parameterize by default. But watch for **raw queries**. `User.raw("SELECT * FROM users WHERE id = " + id)`—that's vulnerable. Or `Model.where("name = '" + input + "'")`—string interpolation in a "safe" ORM still injects. Use the ORM's parameterized methods: `User.where(id: id)`, `Model.where("name = ?", input)`. ORMs protect you when you use them properly. Raw SQL? Be paranoid.

---

## What Could Go Wrong? (Mini Disaster Story)

A small SaaS company had a search box. "Search customers by name." The backend did: `"SELECT * FROM customers WHERE name LIKE '%" + search + "%'"`. A competitor ran a script. Sent `%' UNION SELECT id, password, email FROM users--`. The query returned every user's password hash. 50,000 accounts. The competitor didn't even need to crack hashes—some users had weak passwords. The company found out when users reported strange logins. They had to reset every password, notify users, fix the code, and face a PR nightmare. One concatenation. One UNION. Total compromise. The fix took one line: use parameterized queries. The damage took months to repair.

---

## Surprising Truth / Fun Fact

**SQL injection** has been in the OWASP Top 10 (the list of most critical web vulnerabilities) since the list began—over 20 years. It's still there. Not because we don't know how to fix it. Because developers still concatenate strings. The fix is simple. The discipline is hard. Every major framework supports parameterization. The attack persists because of human error, not technical mystery.

---

## Quick Recap (5 bullets)

- **SQL injection** = user input becomes part of the SQL query, allowing attackers to execute arbitrary SQL.
- **Parameterized queries** = use placeholders. Pass values separately. The database treats input as data, not code.
- **ORMs** = use them. They parameterize by default. Avoid raw SQL with string concatenation.
- **Never trust user input** = assume every input is hostile. Validate. But always parameterize.
- **Real breaches** = Heartland, Sony, countless others. One vulnerability. Massive damage.

---

## One-Liner to Remember

> **Never concatenate user input into SQL. Use parameterized queries. Every time. No exceptions.**

---

## Next Video

You've secured your backend. Parameterized queries. No injection. But your frontend keeps getting blocked by the browser. "CORS error." You're calling your own API. Same project. Why is the browser saying no? What is **CORS**—and why does it exist? That's next.
