# Web CTF Final Unreachable

We’re given a small PHP web‐app:

```php
<?php
require("flag.php");

if (isset($_GET['source'])) {
    highlight_file(__FILE__);
    die();
}

if (isset($_GET['whoIsTheBest'])) {
    $your_str    = $_GET['whoIsTheBest'];
    $another_str = 'SecuritySummerSchool';
    $final_str   = preg_replace("/$another_str/", '', $your_str);
    if ($final_str === $another_str) {
        echo unreachable();
    }
}

?>
```

Visiting ?source shows us the source.

The goal is to trigger the unreachable() call (which prints the flag), by satisfying:

```php
preg_replace("/SecuritySummerSchool/", '', $your_str) === 'SecuritySummerSchool'
```

- *What does preg_replace do?* **It removes all non‐overlapping occurrences of the regex pattern—in our case the literal string "SecuritySummerSchool"—from $your_str.**

- *What condition must hold?* **After stripping out every "SecuritySummerSchool", the remainder must equal exactly "SecuritySummerSchool".**
  
So the text will only be deleted once we did: **SecuritySummerSchooSecuritySummerSchooll**. Link: http://ctf-13.security.cs.pub.ro:8003/?whoIsTheBest=SecuritySummerSchooSecuritySummerSchooll

Flag: **SSS{y0u_r3ach3d_th3_unr3ach4bl3}**
