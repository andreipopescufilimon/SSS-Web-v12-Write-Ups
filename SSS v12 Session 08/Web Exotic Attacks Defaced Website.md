# Web Exotic Attacks Defaced Website

We are greeted with a login form and an image:

```html
<img src="img/d3f4c3d.png" style="height:1px;" />
```

The image returns 404 Not Found. I tried modifying d3f4c3d → removing 3 and 4 → became defaced. THis became **http://141.85.224.99:8005/img/defaced.png**. This worked and revealed a screenshot of PHP source code.

![img](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2008/img08/defaced.png)

This shows that the server compares a user-supplied MD5 hash against a constant — and uses ==, not ===.

In PHP: **"0e123456" == "0"    // true**

So if you input a string that produces an MD5 hash like:

```php
md5("QNKCDZO") = "0e830400451993494058024219903391"
```

PHP treats both "0e830400..." and "0e413229..." as 0, making: ***"0e830400..." == "0e413229..."  // true***

So if we can generate a md5(password . username) that starts with 0e and only digits, PHP will interpret it as 0. We don’t need an exact match — just any value that PHP interprets as zero will pass the comparison.

```bash
echo -n QNKCDZO | md5sum     # 0e830400451993494058024219903391
echo -n 240610708 | md5sum   # 0e462097431906509019562988736854
```

I inputed the as known password on the website and for both **QNKCDZO** and **240610708** etc.. got the flag after login: **SSS{th4nk_y0u_s0_much_f0r_g3tt1ng_my_w3bs1t3_b4ck_d4rl1ng}**
