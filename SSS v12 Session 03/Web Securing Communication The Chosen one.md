# Web Securing Communication The Chosen one.md

We compared all certificates from 1 to 100 with ca_two.crt.

```bash
#!/bin/bash

cd /mnt/c/Users/Andrei/Downloads/certs || exit

for i in {1..100}; do
    if openssl verify -CAfile ca_two.crt "$i.crt" > /dev/null 2>&1; then
        echo "$i.crt: OK"
    else
        echo "$i.crt: FAILED"
    fi
done
```
