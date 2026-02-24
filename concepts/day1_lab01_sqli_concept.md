# Day 1 — My First Realization (SQL Injection)

Today I understood something important:

A website is not the system.

The website is only a surface. Behind it exists logic, and behind that logic exists a database. When a user interacts with a page, the browser is not “doing work” — it is sending instructions to a server, and the server translates those instructions into database queries.

During the lab, the page allowed me to filter products by category. At first this looked harmless. I was simply clicking options like any normal user would.

But the moment I modified the input, the behavior changed.

Instead of sending data, I sent a condition:
`' OR 1=1--`

The system didn’t recognize it as an attack.
It recognized it as a command.

The application trusted my input and directly inserted it into its SQL query. Because `1=1` is always true, the database returned every product, including the hidden ones. I didn’t bypass the interface — I changed the logic that controlled the interface.

That was the realization:

I didn’t hack the website.
I spoke to the database through the website.

Security vulnerabilities are not caused by complex hacking techniques. They are caused by misplaced trust. The application assumed user input would always be data, but I provided instructions instead.

From now on, I will not look at websites as pages.

I will look at them as systems that accept input, process logic, and reveal behavior — and every place that logic depends on user input is a possible weakness.
