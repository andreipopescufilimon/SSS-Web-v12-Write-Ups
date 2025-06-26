# Web: Cookies and Session Management: Do you need glasses?

## Page Source Investigation
When loading the page in the browser and inspecting the <head> of the HTML, we see this unusual meta tag:

```html
<meta content="anVreG9xbm5jYQ==" name="password">
```

This clearly looks like Base64. We decode it:

```bash
echo "anVreG9xbm5jYQ==" | base64 -d
```

Output: jukxoqnnca
Password recovered: jukxoqnnca

## Finding the Login Endpoint
We test login using common paths:

```bash
curl -s -X POST -F 'username=admin' -F 'password=jukxoqnnca' http://141.85.224.70:8086/login.php
```

This responds with:

```json
{"redirect":"staff.php"}
```

Login is successful and sets a PHP session cookie. We save it for reuse:

```bash
curl -s -c cookie.txt -X POST \
  -F 'username=admin' \
  -F 'password=jukxoqnnca' \
  http://141.85.224.70:8086/login.php > /dev/null
```

We then try to access the restricted staff page:

```bash
curl -s -b cookie.txt http://141.85.224.70:8086/staff.php
```

## No Flag… or Is It?

Initially, we tried extracting the flag with a simple grep:

```bash
curl -s -b cookie.txt http://141.85.224.70:8086/staff.php | grep -o 'SSS{.*}'
```

Nothing was returned.

We manually inspected the HTML response and noticed something strange embedded deeper in the code. The string:

```
MMM{1_ly4ffs_f1ey_f34p1ha_nl4w3m_1h_w0gg3hnm}
```

was hidden — possibly in a comment, disguised element, or strange part of the HTML. But not in the typical SSS{} format.

## Decoding MMM{...}
We realized this was a Caesar cipher — specifically a +6 letter shift.

To decode it, we used:

```bash
echo "MMM{1_ly4ffs_f1ey_f34p1ha_nl4w3m_1h_w0gg3hnm}" | tr 'A-Za-z' 'G-ZA-Fg-za-f'
```

Output: **SSS{1_re4lly_l1ke_l34v1ng_tr4c3s_1n_c0mm3nts}**
