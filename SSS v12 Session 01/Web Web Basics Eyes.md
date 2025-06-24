# Web: Web Basics - Eyes

## Description
We're given a basic Apache2 default page at:

http://141.85.224.70:8081/eyes/

## Step 1: Recon and Observation

The page loads the default Apache welcome screen:

```bash
curl -s http://141.85.224.70:8081/eyes/
```

Observation: It uses the default index.html file from /var/www/html.

## Step 2: Directory and File Fuzzing with ffuf

We fuzzed for hidden files and common extensions:

```bash
ffuf -u http://141.85.224.70:8081/eyes/FUZZ -w ./common.txt -e .txt,.html,.bak,.old,.php -mc 200,403
```

Results:

```bash
.hta.txt                [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 28ms]
.hta                    [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 28ms]
.hta.html               [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 27ms]
.hta.old                [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 28ms]
.hta.bak                [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 28ms]
.hta.php                [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 27ms]
.htaccess               [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 28ms]
.htaccess.txt           [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 28ms]
.htaccess.html          [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 28ms]
.htaccess.old           [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 27ms]
.htaccess.bak           [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 29ms]
.htaccess.php           [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 30ms]
.htpasswd               [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 27ms]
.htpasswd.txt           [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 28ms]
.htpasswd.html          [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 28ms]
.htpasswd.bak           [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 28ms]
.htpasswd.php           [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 27ms]
.htpasswd.old           [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 27ms]
index.html              [Status: 200, Size: 10739, Words: 3431, Lines: 370, Duration: 34ms]
index.html              [Status: 200, Size: 10739, Words: 3431, Lines: 370, Duration: 29ms]
```

![img1](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2001/images-s1/eyes1.jpg)

index.html → 200 OK (default Apache page)
.htaccess, .htpasswd, .hta, etc. → 403 Forbidden

No other useful files discovered.

## Step 3: Targeted CTF File Brute-force

We created a minimal wordlist.txt wordlist:
```
flag
flag.txt
theflag
theflag.txt
the_flag.txt
the_flag.html
sss
sss.txt
flag.html
flag.php
hidden
eyes
eyes.txt
secrets
secrets.txt
```

And ran:

```bash
ffuf -u http://141.85.224.70:8081/eyes/FUZZ -w ./wordlist.txt -mc 200,403
```

Only result:

```css
index.html              [Status: 200]
```

## Step 4: Inspect index.html Directly
We finally checked the raw contents of index.html for any hidden comments:

```bash
curl -s http://141.85.224.70:8081/eyes/index.html | grep -i sss_ctf
```

Found the flag in a comment:

![img2](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2001/images-s1/eyes2.jpg)

```
/* SSS_CTF{almost_in_plain_site} */
```
