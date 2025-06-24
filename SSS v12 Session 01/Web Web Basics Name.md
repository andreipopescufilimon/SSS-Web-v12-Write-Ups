# Web: Web Basics: Name

## Description:

The webpage shows a simple message and a hint:
"It’s not complicated. Get the_flag."

This suggests that a file named the_flag (likely the_flag.html) might be accessible directly.

## Exploitation:

Enumerate files with ffuf to find hidden resources:

```bash
ffuf -u http://141.85.224.70:8089/name/FUZZ -w /usr/share/wordlists/dirb/common.txt
```

Result:

```bash
/the_flag.html         [Status: 200, Size: 42]
```

Access the file with curl:

```bash
curl http://141.85.224.70:8089/name/the_flag.html
```

Output:

```
<p>SSS_CTF{my_name_is_who}</p>
```
