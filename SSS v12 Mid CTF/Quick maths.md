# Quick maths


By any luck we first typed in number 69 and then we got this googolplex.php -> then we entered to this link : http://141.85.224.118:8082/googolplex.php getting this code: 

```php
$secret = 736089 + 2 ** 21;
$secret = ((($secret ?? $secret * 3) * 40 - 1234) * 2 - 100) * 2;

if (isset($_SERVER['HTTP_REFERER']) && $_SERVER['HTTP_REFERER'] == $secret) {
	$flag = 'SSS_CTF{...}';
}
```

And its a mathematic problem: 

```php
$secret = 736089 + 2 ** 21; // = 2833241
$secret = ((($secret ?? $secret * 3) * 40 - 1234) * 2 - 100) * 2; // math
```

**$secret = 453313424**


And then we curl it **curl http://141.85.224.118:8082/ -H "Referer: 453313424"** and get the flag: **SSS{0ne_plus_0ne_1s_tw0}**.
