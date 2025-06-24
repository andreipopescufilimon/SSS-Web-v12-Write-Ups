# Web: Web Basics: King-Kong

## Description:

Accessing the page shows just: **I only answer to King-Kong!**
This means if I don’t set the User-Agent to King-Kong, the server will refuse to give me the
flag.
**Exploit** :

```
curl -A "King-Kong" http://141.85.224.70:8086/king-kong/
```

![image](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2001/images-s1/king-kong.jpg)
  
=> **flag is SSS_CTF{godzilla_got_nothing_on_me}</p>\n**


