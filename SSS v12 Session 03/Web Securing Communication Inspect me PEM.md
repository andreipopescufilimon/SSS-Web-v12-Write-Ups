# Web: Cookies and Session Management: Beep beep boop

Get the flag from beep-boop-boop.

We first observe that beep boop boop its a sound or wave.
In the source code of the page, in the header we have "Try somewhere else ... beep boop boop".
Then we checklist the paget with boop and beep **http://141.85.224.70:8096/boop** or **http://141.85.224.70:8096/beep** and then 
**http://141.85.224.70:8096/robots.txt**.

Then we get the page: **http://141.85.224.70:8096/73656372657420666f72204153494d4f.php** and modify the cookie to ASIMOV to get the right flag:
Here is your secret: SSS{We_w0rsh1p_1saac_As1m0v}

```
73656372657420666f72204153494d4f
73 65 63 72 65 74 20 66 6f 72 20 41 53 49 4d 4f
s  e  c  r  e  t     f  o  r     A  S  I  M  O
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>YouniHack 2025 Registration Confirmation</title>
</head>
<body style="margin: 0; padding: 0; background-color: #0e0f1a; font-family: 'Courier New', monospace; color: #ffffff;">
  <div style="max-width: 600px; margin: auto; padding: 30px 20px; background-color: #12131f; border-radius: 8px; box-shadow: 0 0 15px #00ffcc33;">
    <center>
      <pre style="font-size: 18px; color: #00ffcc; margin: 0 0 20px 0;">
╔═════════════════════╗
║    YouniHack 2025   ║
╚═════════════════════╝
      </pre>

      <h2 style="color: #ffffff; margin-top: 0;">🎉 Registration Confirmed!</h2>
      <p style="font-size: 16px; color: #ffffff; margin-bottom: 30px;">You're now officially registered for <strong>YouniHack 2025</strong>. Welcome to the cyber battlefield!</p>

      <table style="width: 100%; font-size: 14px; background-color: #1c1d2e; border: 1px solid #2a2b3c; border-radius: 6px; margin-bottom: 30px; color: #ffffff;">
        <tr><td style="padding: 10px;"><strong>🗓️ Event:</strong></td><td>YouniHack 2025 - Attack & Defense</td></tr>
        <tr><td style="padding: 10px;"><strong>📅 Date:</strong></td><td>September 8, 2025</td></tr>
        <tr><td style="padding: 10px;"><strong>📍 Location:</strong></td><td>Universitatea Politehnica București</td></tr>
        <tr><td style="padding: 10px;"><strong>🎮 Format:</strong></td><td>In-Person</td></tr>
      </table>

      <h3 style="color: #ffffff;">🪪 Your Check-In QR Code</h3>
      <img src="data:image/png;base64,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" alt="QR Code" style="width: 200px; height: 200px; border-radius: 8px; border: 4px solid #00ffcc; margin: 20px 0;">

      <p style="font-size: 14px; color: #ffffff;">Present this code at the registration desk to check in.</p>

      <hr style="border: 1px solid #2a2b3c; margin: 30px 0;">

      <h3 style="color: #ffffff;">📅 Event Lineup</h3>
      <ul style="text-align: left; padding-left: 20px; font-size: 14px; color: #ffffff;">
        <li><strong>Web Vulnerabilities Conference</strong> — Sep 8, 1h</li>
        <li><strong>Frontend Security Conference</strong> — Sep 8, 1h</li>
        <li><strong>Backend Security Conference</strong> — Sep 8, 1h</li>
        <li><strong>CTF Prep Workshop</strong> — Sep 8, 2h</li>
        <li><strong>Main Hackathon</strong> — Sep 8, 24h</li>
      </ul>

      <p style="font-size: 14px; color: #ffffff;"><strong>🎫 Slot Reserved:</strong> 1 of 400 registered participants</p>

      <hr style="border: 1px solid #2a2b3c; margin: 30px 0;">

      <p style="font-size: 14px; color: #ffffff;"><strong>🔐 SYSTEM STATUS:</strong> OPERATIONAL</p>
      <p style="font-size: 14px; color: #ffffff;"><strong>🛡️ SECURITY LEVEL:</strong> MAXIMUM</p>
      <p style="font-size: 14px; color: #ffffff;"><strong>🔒 CONNECTION:</strong> ENCRYPTED</p>

      <hr style="border: 1px solid #2a2b3c; margin: 30px 0;">

      <p style="font-size: 13px; color: #ffffff;">📫 Questions? Contact us at <a href="mailto:info@younihack.org" style="color: #00bfff;">info@younihack.org</a></p>
      <p style="font-size: 13px; color: #ffffff;">🔗 Follow us: <a href="https://x.com/younihack" style="color: #00bfff;">@younihack</a></p>

      <p style="font-size: 11px; color: #888888; margin-top: 40px;">© 2025 YouniHack. All rights reserved.</p>
    </center>
  </div>
</body>
</html>
