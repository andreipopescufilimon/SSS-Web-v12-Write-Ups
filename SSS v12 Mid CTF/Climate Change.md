# Climate Change


We have the initial site **http://141.85.224.118:7000/** and then we observe that we need to find another endpoint, then we git a list of endpoints from git to test and find out that **http://141.85.224.118:7000/.env** is the new endpoint.

Then after the new endpoint we find out that we have:

```
CUCHI_ENC=affine
CUCHI_A=133
CUCHI_B=7
CUCHI_VAL=cheesecake
```

So then we figure out that it's about changing a cookie, so then we do a mathematic : **A=133, B=7 and mod 26**

So **E(x) = (A x x + B) mod m** 

**A** and **B** are the keys, **m** is the module ( **26** ) and x is the numeric interpretation of characters.

Then we have the flag **ncttjtnhlt**.
