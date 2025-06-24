# Web: Web Basics: Give to Get

## Description

The challenge page hints:  
> "You have to **ask** for the **flag** to **get** it!"

This suggests a hidden query parameter is needed.

---

## Exploitation

By appending `?ask=flag` to the URL, we "ask" the server correctly:

```bash
curl "http://141.85.224.70:8084/give-to-get/?ask=flag"

![img](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2001/images-s1/give-to-get.jpg)
