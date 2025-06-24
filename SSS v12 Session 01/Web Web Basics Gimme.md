# Web: Web Basics: Gimme

## Description

Tried to make a request in console of type GET then I received the error 405, so GET wasn’t good. 

## Vulnerability

I made a request of type POST with payload of 35, but wasn’t enough.

## Exploitation

Then I added the payload ‘get_flag’ and then I got the flag. 

```bash
fetch("/gimme", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    name: "35",
    get_flag: "get_flag"
  })
})
.then(res => res.text())
.then(console.log)
.catch(console.error);
```
