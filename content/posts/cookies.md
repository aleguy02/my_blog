+++
date = '2025-08-29T13:56:59-04:00'
draft = false
title = 'Form Validation and Cookies 🍪'
+++

I recently updated how [GraphUF](https://github.com/aleguy02/graph-uf), one of my personal projects, handles form inputs. Before, form inputs were joined and converted into a CSV string, then passed as a query parameter to a redirect. The backend then parsed the CSV string and validated each value with regex. After speaking with Adhip, one of my MLH mentors working at Meta, I learned of a better approach: input validation in the frontend and using a cookie to pass around values.

**Why do form validation in the frontend?**  
Parsing and validating CSV in the backend adds unnecessary overhead, especially at scale. More importantly, it ties up backend resources that could be spent on actual business logic. A better approach would be to offload this responsibility to the user's machine, i.e. in the frontend. The time it takes to validate one form is negligible, so the user won't notice. The time it takes to validate hundreds of forms per second is much greater, and could affect latency. I don't expect my app to have this sort of traffic, but in general, it is a good practice to build reliable systems from the start.

Another reason was that I could give users feedback if their inputs were invalid much more quickly and easily. No more silent failures or mysterious refreshes.

**Why use a cookie?**  
As a user of the app myself, I found it extremely annoying to have to input all of my completed courses into the form every time I wanted to make a change to the OTHER form parameters. It was a huge waste of time, and I couldn't see real users getting past this blocker. If there was some way to preserve the courses that I had already input, it would be a much better experience. Enter the session cookie: a lightweight way to store non-sensitive session information directly in the browser.

With a cookie, I could pass around data anywhere in the app with ease. No more having to rely on a brittle query parameter. It made the URLs much more appealing. And most importantly, I could auto-populate the main form with previous inputs. Now, if a user wanted to, say, add a course they forgot to originally input, they wouldn't have to add EVERY course from scratch, just the one they missed.

Also, courses could then be stored in a JSON-like array instead of a CSV string, a cleaner and more efficient format.

**In conclusion,** these changes led to code that was faster, more readable, more reliable, and more maintainable. If you want to see the diff of these changes, check out this [commit](https://github.com/aleguy02/graph-uf/commit/f93db8bcba76451f0043af178a9c9015df8c3300)!