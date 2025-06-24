# Web Basics: Cockroach

**Method Not Allowed** => This means the default HTTP method (GET) isn’t allowed.

## Step 1: Check allowed HTTP methods

We use the OPTIONS method to see what the server accepts:

```bash
curl -X OPTIONS http://141.85.224.70:8080/cockroach -i
```

The server replies with:

```bash
Allow: DELETE, OPTIONS
```

![img1](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2001/images-s1/cockroach1.jpg)

So DELETE is allowed.

## Step 2: Use the DELETE method

Send the following:

```bash
curl -X DELETE http://141.85.224.70:8080/cockroach
```

![img2](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2001/images-s1/cockroach2.jpg)
