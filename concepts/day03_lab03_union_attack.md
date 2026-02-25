# Day 3 — When the Database Started Talking Back

Today SQL injection stopped feeling like a trick and started feeling like communication.

In the earlier labs, I was only changing behavior — bypassing login or showing hidden products.

In this lab, the objective was different:
not to break the application,
but to discover what database the server was running.

That confused me at first.

I wondered:
How can a website reveal the type of database behind it?

---

## The First Clue

The page filtered products using a URL parameter:

category=Gifts

I inserted a single quote:

'

The application produced an error.

This was important.

The error meant my input was not just being displayed — it was being interpreted by a database query.

The application was passing my input directly into SQL.

---

## A New Idea

I realized something:

The website queries the database to fetch products.

If my input becomes part of the query,
then I can change not only the result…
but the **question being asked to the database**.

I was no longer limited to product data.

I could ask the database for its own information.

---

## Understanding Column Count

I was instructed to try:

' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--

At first this looked like guessing.

Then I understood what was happening.

`ORDER BY` sorts results using a column number.

If I request a column that does not exist, the database throws an error.

So the error revealed structure.

I wasn’t randomly trying numbers.

I was measuring how many columns the original query returned.

---

## What UNION Really Means

Then I learned about the UNION operator.

UNION joins the results of two SQL queries.

Normal query:

SELECT name, description
FROM products
WHERE category='Gifts'

Injected query:

SELECT name, description
FROM products
WHERE category=''
UNION
SELECT banner, NULL FROM v$version--

The database executes both queries and merges the output.

The website believes it is showing products.

But the database is now returning its internal information.

---

## Why Oracle Needed v$version

This lab used an Oracle database.

Unlike some databases, Oracle requires selecting from a real table.

The table `v$version` stores the database version.

When I injected:

' UNION SELECT banner, NULL FROM v$version--

the page displayed Oracle version text.

At that moment I understood:

I was not interacting with the application anymore.

I was interacting directly with the database server.

---

## Questions I Had & What I Learned

### Why did we need the column number first?

Because UNION only works if both queries return the same number of columns.

If the structure does not match, the database rejects the query.

So `ORDER BY` helped me discover the structure before inserting my own query.

---

### Why was NULL used?

I only needed one column to display text.

The remaining columns still had to exist, so I filled them with NULL values.

NULL satisfies the database format without affecting the output.

---

### How is this different from earlier SQL injection?

Earlier labs:
I changed logic.

This lab:
I extracted information.

The database was no longer just verifying input.

It was answering my queries.

---

## What I Learned

• SQL injection can read database information
• Database errors reveal internal structure
• UNION combines attacker queries with real queries
• Column matching is required for UNION injection
• System tables store sensitive metadata

---

## My Takeaway

Before today, I thought SQL injection was about bypassing restrictions.

Now I understand it is about controlling queries.

The website is only a messenger.

If I control the SQL query, I control what the database says back.

Today was the first time I didn’t feel like I fooled the application.

I felt like I questioned the database.
