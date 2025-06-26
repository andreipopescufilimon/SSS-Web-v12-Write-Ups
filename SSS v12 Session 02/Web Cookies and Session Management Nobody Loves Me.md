# Web: Cookies and Session Management: Nobody Loves Me

Upon visiting the URL, everything appears like a generic Bootstrap template. Nothing unusual is visible in the UI or HTML.
However, while inspecting the JavaScript at the end of the page, we find this suspicious function:

```js
function ernqsvyr() {
  $.ajax({
    url: './ernq-svyr.php',
    dataType: "text",
    success: function(data){
      console.log(data);
    }
  });
}
```

The function name ernqsvyr and filename ernq-svyr.php appear ROT13-encoded.

- ernqsvyr → readfile

- ernq-svyr.php → read-file.php

## Exploit:

Opened browser console and ran:

```js
ernqsvyr()
```

This triggers a request to read-file.php and logs the response to the console.

![img](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2002/images-s2/love.png)
