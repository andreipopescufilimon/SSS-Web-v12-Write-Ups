# Tangled

On inspection, the page contains a JavaScript maze functions named after U.S. presidents (johnson, nixon, ford, carter, reagan, etc.), with various code paths and distractions0. The submit button doesn’t send any real HTTP request—everything is client-side JavaScript.

## 1. Form Submission Blocked

The form looks like this:

```html
<form id="form1" action="" method="post" onsubmit="return johnson()">
```
  
The onsubmit calls the **johnson()** function. But this function never returns true or triggers a real submission.

```js
function johnson() {
	const flags = 'aXRzIGEgbG9uZyBqb3VybmV5IHlvdW5nIHBhZGF3YW4=';  // base64
	hidden(file, password, key);  // does nothing
	return nixon(flags);
}
```

The flags variable is a distraction:

```nginx
Base64 decode: "its a long journey young padawan"
```

But it’s never used. Instead, execution flows through many functions like:

```js
nixon() → ford() → carter() → reagan() → bush() → clinton() → bushv2() → obama()
```

All of these just print to console or call the next dead-end function. No data is sent anywhere.

## 2. The /hillary Endpoint

One unused function stands out:

```js
function the_last_one() {
	var elem = document.getElementById('form1');
	elem.action="/hillary";
}
```

This suggests that the actual flag logic might reside at the **/hillary** endpoint, but it's never called in the normal code flow.

## 3. Manual POST to /hillary

Since nothing is being submitted by default, we can simulate a real request manually.

We use curl to send data to the hinted endpoint:

```bash
curl -X POST http://141.85.224.118:5010/hillary -d "message=test"
```

Response: **SSS{living_1n_am3rica}**
