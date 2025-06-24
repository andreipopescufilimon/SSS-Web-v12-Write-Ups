# Web: Web Basics: Surprise

## Step 1: Identify allowed HTTP methods

We tested several common HTTP methods using curl:

```
curl -X GET http://141.85.224.70:8093/surprise          # 405 Method Not Allowed
curl -X POST http://141.85.224.70:8093/surprise         # 405 Method Not Allowed
curl -X DELETE http://141.85.224.70:8093/surprise       # 405 Method Not Allowed
curl -X PATCH http://141.85.224.70:8093/surprise        # 405 Method Not Allowed
curl -X PUT http://141.85.224.70:8093/surprise          # Response: "I don't understand you :("
```

![img1](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2001/images-s1/surprise-1.jpg)

*Observation:* Only PUT is accepted but requires data to be meaningful.

## Step 2: Send form data
We attempted sending a form field:

```
curl -X PUT -d "surprise=flag" http://141.85.224.70:8093/surprise
```

Response:

![img2](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2001/images-s1/surprise-2.jpg)

This implies that the server wants data in JSON format, not application/x-www-form-urlencoded.

## Step 3: Send JSON data
We then tried sending a JSON body:

```
curl -X PUT -H "Content-Type: application/json" -d '{"surprise": "flag"}' http://141.85.224.70:8093/surprise
```

Response:

![img3](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2001/images-s1/surprise-3.jpg)

This indicates the expected key is now "name" instead of "surprise".

Step 4: Provide expected name
We tried sending a name key with the value "King-Kong" — a reference to another challenge in the same CTF:

```
curl -X PUT -H "Content-Type: application/json" -d '{"name": "King-Kong"}' http://141.85.224.70:8093/surprise
```

Response:

![img4](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2001/images-s1/surprise-4.jpg)
