# Web CTF Final Treasure

### SQLi Detection

Using `sqlmap` on the login request, specifically targeting the `username` parameter, I discovered a SQL injection of type:
- **boolean-based blind**
- **error-based**
- **time-based blind**

```bash
sqlmap -u "http://ctf-13.security.cs.pub.ro:8009/index.php" \
  --data="username=aaa&password=bbb" \
  --level=5 --risk=3 --batch --dbs
```

---

### Extracting Databases and Tables

SQLMap identified the DBMS as MySQL, and I extracted two databases:

- `information_schema`
- `treasure`

Inside the `treasure` database, I found two interesting tables:

- `users`
- `keyz`

---

### Extracting Credentials

I dumped the contents of the `users` table using:

```bash
sqlmap -D treasure -T users --dump
```

With the credentials obtained, I logged into the web application.

---

### Decoding the Encrypted File

After logging in, the application returned a Base64-encoded file. I decoded it using:

```bash
base64 -d baza.txt > fisier
```

This resulted in a binary file that appeared to be encrypted — not plain text.

---

### The `keyz` Table with 20 RSA Private Keys

I recalled the `keyz` table contained 20 RSA private keys. I extracted and saved all keys into `.pem` files and wrote a script to try each of them on the `fisier` file:

```bash
openssl rsautl -decrypt -inkey keyX.pem -in fisier -out flag.txt
```

---

The 21st key get us the flag!
