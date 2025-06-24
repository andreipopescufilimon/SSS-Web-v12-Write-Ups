# Web: Web Basics – Lame Login

## Description

We're given a simple login form at /login:

```html
<form action="/login" method="get">
    Username:<input type="text" name="username">
    Password:<input type="text" name="password">
</form>
```

Upon inspecting the page source, we find a suspicious HTML comment:

```html
<!-- TODO - remove the line below
     d033e22ae348aeb5660fc2140aec35850c4da99762d5a7eab7c13e99e355dd05b0377a6d01a8fa99 -->
```

This 80-character string suggests a concatenation of two SHA1 hashes, each 40 characters long.

## Step 1: Analyzing the hash

We split the hash:

```nginx
d033e22ae348aeb5660fc2140aec35850c4da997
62d5a7eab7c13e99e355dd05b0377a6d01a8fa99
```

Using [https://crackstation.net/](https://crackstation.net/), we found:

d033e22ae348aeb5660fc2140aec35850c4da997 → admin

62d5a7eab7c13e99e355dd05b0377a6d01a8fa99 → Password123$

![admin](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2001/images-s1/login1.jpg)
![Password123$](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2001/images-s1/login2.jpg)

This suggests the backend verifies login by checking:

```php
if (sha1(username) . sha1(password) == stored_hash)
```

## Step 2: Exploiting the login

Now that we know:
- username = admin
- password = Password123$

We login:
```bash
curl "http://141.85.224.70:8087/login?username=admin&password=Password123$"
```

![flag](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2001/images-s1/login3.jpg)
