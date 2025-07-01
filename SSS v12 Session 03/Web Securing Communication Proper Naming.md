# Web Securing Communication Proper Naming.md

Visiting the URL shows a minimal HTML page:

```html
<!DOCTYPE html>
<html>
	<head>
		Not here
	</head>
	<body>
		Call smokey.burger.com
	</body>
</html>
```

This hints that the flag is not on the current virtual host, but rather on another one:
**smokey.burger.com**.

Since smokey.burger.com isn’t a public domain, the site is likely using virtual hosting—a technique where the server serves different content depending on the Host header of the HTTP request. So we made a request using curl:

```bash
curl -H "Host: smokey.burger.com" http://141.85.224.117:3280/
```

The server responded with:

```html
<!DOCTYPE html>
<html>
    <head>
        Flag
    </head>
    <body>
        SSS{who_you_wanna_kill}
    </body>
</html>
```
