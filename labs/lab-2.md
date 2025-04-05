## 🔍 Lab 2: SQL Injection – Lateral Movement via Leaky API

### 💼 Scenario
A helpdesk system fetches user profiles using a numeric ID but exposes raw DB responses without sanitation.

### 🎯 Objective
There are 5 users in the database, with id's from 1 to 5. Your mission... to steal their passwords via SQLi. Use SQL Injection to enumerate DB tables, dump data, and escalate.

### 🧪 Steps
1. Access DVWA → SQL Injection

2. We know from the `Objective` that the database contains five users with numerical IDs. Performing ordinary SQL queries on these user IDs gives us their first names and surnames.

3. Checking the source code from "View Source" button we find that the underlying SQL query statement is:

```sql
SELECT first_name, last_name FROM users WHERE user_id = '$id'
```
The SQL query to return $first and $last fields.

4. Let’s manipulate the text form input denoted by `$id` and explore ways to escape it to get the passwords.

5. Extract data on all the five users in the database with the input `%' or '0'='0`, which yields the following unsanitized SQL query where `%` is a wildcard character, `'0'='0'` is a tautology, and the opening and closing straight apostrophes `'`match in the underlying PHP code, so that it becomes:

```sql
SELECT first_name, last_name FROM users WHERE user_id = '%' or '0'='0';
```

6. If a simple tautology can escape the query, so can any SQL keyword from the user. In particular, the `UNION` keyword in SQL combined with a suitable `SELECT` query will expose the underlying database. We call this approach a `SQL injection UNION attack`.

7. The [MySQL documentation](https://dev.mysql.com/doc/refman/8.4/en/information-schema.html) reveals that a table called `information_schema` stores information about the database containing the `users` table. This table gives us a clue as to where to find the passwords. We also want to ensure we assign values to the two variables `$first` and `$last` by returning two columns, not one.

8. We assign to `$id` the text input of `%' UNION SELECT table_name,column_name FROM information_schema.columns #`, where `#` marks the beginning of a comment (`';`). Thus, the SQL query becomes:

```sql
SELECT first_name, last_name FROM users WHERE user_id = '%' UNION SELECT table_name,column_name FROM information_schema.columns #';
```

9. Your penultimate strike is to capture the passwords while returning two data columns. Here, we choose the text input of `%' UNION SELECT user, password FROM users #` into our vulnerable page.

```sql
SELECT first_name, last_name FROM users WHERE user_id = '%' UNION SELECT user, password FROM users #';
```

10. We’ve found password hashes instead of plain-text passwords. Cracking passwords is outside the scope of this introductory DVWA guide.


### 🧠 Real-World Context
APIs that expose DB queries directly are common in legacy web services. SQLi is still one of the top OWASP vulnerabilities.

### 🛡️ Mitigation
- Always use parameterized queries or ORMs.
- Disable verbose error messages in production.
- Employ WAFs (Web Application Firewall) to block suspicious queries.
