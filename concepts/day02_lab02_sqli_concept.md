# Day 2 — Seeing the Network Behind the Website

Today I learned something important:

The browser is not where authentication happens.

Authentication happens in the network.

Initially, I was confused why the lab required Burp Suite. I thought hacking meant interacting with the login page directly. But once I enabled interception, I realized I was no longer dealing with a webpage — I was dealing with traffic.

The first request I captured was:

GET /login

Nothing useful appeared. No username or password.
This showed me that loading a page and logging in are two completely different actions.

The login page only displays the interface.

The real authentication occurs when the browser sends a POST request.

After submitting dummy credentials, Burp captured:

username=a&password=a

For the first time, I could see exactly what my browser was sending to the server.

The server does not see buttons, forms, or pages.
It only sees this line of text.

---

## Understanding Burp Suite

Burp Suite works as a man-in-the-middle proxy.

Normal communication:
Browser → Server

With Burp:
Browser → Burp → Server

Burp allowed me to intercept and modify the request before it reached the server. Instead of typing into the login page, I could directly edit the message sent to the backend application.

I was no longer limited by the interface.

I could control what the server believed I sent.

---

## Questions I Had & What I Learned

### Why is `'` used?

In SQL queries, user input is placed inside quotation marks.

Example query:
SELECT \* FROM users WHERE username = 'input';

The `'` character closes the string.
By adding `'`, I exited the normal data and entered the query itself.

So `'` is not random — it breaks out of the expected text value.

---

### Why is `--` used?

`--` is a SQL comment symbol.

Anything written after `--` is ignored by the database.

Original logic:
username='administrator' AND password='something'

After injection:
username='administrator'-- AND password='something'

The database ignores everything after `--`.
This removes the password verification.

So I didn’t guess the password.
I removed the password check.

---

### Why does `' OR 1=1--` work?

`1=1` is always TRUE.

So the database condition becomes:
username='' OR TRUE

If any condition in a WHERE clause is TRUE, the database returns a result.

That means login succeeds even without valid credentials.

---

## What I Learned

• Websites communicate through HTTP requests
• Login pages only create requests, they do not authenticate
• SQL injection works by changing query logic, not by guessing passwords
• `'` exits a text string
• `--` cancels the remaining query
• `1=1` forces a TRUE condition

---

## My Takeaway

Before today, I interacted with websites visually.

Now I understand a website is only a visual layer over a network conversation between a client and a server.

Once I intercepted the login request, I was no longer using the application.

I was observing how it actually works.

Cybersecurity is not about the interface.

It is about understanding and manipulating communication and logic.
