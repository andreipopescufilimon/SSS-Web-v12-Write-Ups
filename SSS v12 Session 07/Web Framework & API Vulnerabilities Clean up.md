# Web Framework & API Vulnerabilities Clean up

The main page loads a Bootstrap table via JavaScript:

```js
$.ajax({
    type: "GET",
    url: "api-v3/get-user-records.php",
    ...
```

This requests **/api-v3/get-user-records.php**, which returns a JSON array of 100 user records.

Suspecting hidden functionality, we fuzzed the endpoint for debug or leftover params using ffuf:

```bash
ffuf -u http://141.85.224.115:7004/api-v3/get-user-records.php?FUZZ=1 \-w fuzz.txt
```

Interesting params: flag, debug, admin, token — all returned 200 OK, but no visible difference in response.

Based on the challenge title, we guessed older endpoints might be present:

```bash
curl "http://141.85.224.115:7004/api-v1/get-user-records.php" | grep -Eo 'SSS\{[^}]+\}'
```

Flag: **SSS{d0nt_f0rget_t0_clean_y0ur_h0use}**
