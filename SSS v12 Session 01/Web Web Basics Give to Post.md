# Web: Web Basics: Give to Post

## Description

From description "You have to ask for the flag to post it", with ask, flag and post bolded.

## Vulnerability

I thought about trying to make a POST request to the server with the payload ask=flag.

## Exploitation

```bash
fetch("/give-to-post/", {
  method: "POST",
  headers: {
    "Content-Type": "application/x-www-form-urlencoded"
  },
  body: "ask=flag"
})
.then(res => res.text())
.then(console.log)
.catch(console.error);
```
