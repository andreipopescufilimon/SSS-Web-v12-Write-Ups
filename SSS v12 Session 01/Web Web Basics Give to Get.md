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
