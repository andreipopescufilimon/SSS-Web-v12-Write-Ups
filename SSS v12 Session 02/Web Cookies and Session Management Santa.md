# Web: Web Cookies and Session Management Santa

## Initial Page Inspection

Use curl to view the raw HTML content of the challenge page:

```bash
curl -s http://141.85.224.70:8083/santa/
```

The HTML shows a countdown element with data-count="2021/12/31" and includes several JavaScript files, notably main.js.

## Inspect JavaScript Execution Behavior

We saw this line in the JavaScript code (within the jQuery wrapper):

```js
$(".countdown").ready(function () {
  return atob("U1NTe2NocjFzdG00c19jNG0zX2U0cmx5X2gzX2hlX2gzfQo=");
});
```

This suggests the flag is Base64-encoded and revealed upon page/script readiness. We want to simulate this decoding without visually reading the source.

## Use Command-Line Base64 Decoder

Decode the base64 string using echo and base64:

```bash
echo "U1NTe2NocjFzdG00c19jNG0zX2U0cmx5X2gzX2hlX2gzfQo=" | base64 -d
```

Output: **SSS{chr1stm4s_c4m3_e4rly_h3_he_h3}**
