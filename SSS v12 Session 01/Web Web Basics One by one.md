# Web: Web Basics: One by one

## Description:

Accessing the page shows just: **<p>S</p>**
Refreshing the page again changes the letter to the next one, e.g., S, S, {, ... suggesting that
the flag is revealed one character at a time, in order, per session.

## Exploit

Since the flag builds incrementally in the same session, we must:
● Use a persistent session (cookie-based).
● Fetch the page repeatedly to accumulate characters.
**●** Stop when the flag is fully revealed (ends in }).

## Python Script

![image](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2001/images-s1/one-by-one.jpg)

```python
import requests

URL = "http://141.85.224.70:8090/one-by-one/"
session = requests.Session()

flag = ""

while True:
    r = session.get(URL)
    if r.status_code == 200:
        start = r.text.find("<p>") + 3
        end = r.text.find("</p>")
        char = r.text[start:end].strip()
        flag += char
        print(flag, end="\r")

        if "}" in flag:
            break
    else:
        print(f"Error: HTTP {r.status_code}")
        break

print("\nFinal flag:", flag)
```
