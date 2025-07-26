# Web CTF Final Access Denied

Connect here and get the flag. Someone left something behind by mistake.

First we have: **http://ctf-13.security.cs.pub.ro:8080/** with access forbidden, then we have a wordlist and we get to:
**http://ctf-13.security.cs.pub.ro:8080/test/.pass.swo** and here have: **goku:{SHA}0R2e3nBHWdwcHlPdJ7HxF7d0LSY=** =>
the username: `goku` password: `kamehameha`.

We log in but we still have the 403 forbidden error, so we need another endpoint, we then make another wordlist try and we find: **http://ctf-13.security.cs.pub.ro:8080/flag/flag.txt** with **SSS{pickabuu}**.
