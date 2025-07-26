# Web CTF Final Patience

Install proxytunnel:

```bash
sudo apt install proxytunnel
```

Then connect using SSH through the HTTPS proxy:

```bash
ssh -o "ProxyCommand=proxytunnel -E -z -q -p ctf-13.security.cs.pub.ro:8444 -d 127.0.0.1:22" -o StrictHostKeyChecking=no ctf@localhost
```

- -p: proxy host and port
- -d: destination target (internal SSH server)
- -E -z -q: options to support tunneling through HTTPS

When prompted: `ctf@localhost's password:` Type `connecttowin`

Once inside, list files: `ls`. After `cat flag`. And we got **SSS{finally_some_action}**.
