# Web: Web Basics: My Special Name

## Description

The server returns a name when accessed with a valid name-id. Concatenating these
names gives the final flag.

## Exploitation

A python script was used to automate enumeration after we manually tried first 7 ids:

![image](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2001/images-s1/my-special-name.jpg)

```python
import requests

url = "http://141.85.224.70:8088/my-special-name"
names = []

for i in range(1, 400):
    response = requests.get(url, params={"name-id": i})
    content = response.text.strip()

    if "SSS" in content or not content:
        print(f"[{i}] End trigger found: {content}")
        break

    #print(f"[{i}] {content}")
    names.append(content)
```
