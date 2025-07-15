# Web Framework & API Vulnerabilities The Accountant

The webpage shows a dropdown with a few retailer options:

- Emag
- Media Galaxy
- PC Garage

These populate a transaction table via a JavaScript-powered AJAX call. In the HTML source, we notice this:

```html
<!--<option value="flanco">Flanco</option>-->
<!--<option value=""></option>-->
```

So **flanco** exists — but is commented out. This hints that there may be more hidden retailers.

We use Chrome Developer Tools: Open DevTools → Network tab. And there we found:

```https
records.php?retailer=flanco
records.php?retailer=pcgarage
records.php?retailer=mediagalaxy
records.php?retailer=emag
records.php?retailer=
records.php?retailer=flanco
records.php?retailer=altex
```

![img](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2007/images-s7/network.png)

Even though altex is not present in the dropdown, it was queried manually — and it returned valid data.

Visit **http://141.85.224.115:7005/api-v2/retailers/records.php?retailer=altex** and there we found the flag **SSS{1,945,203,333}**.
