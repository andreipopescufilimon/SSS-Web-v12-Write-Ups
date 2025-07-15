# Web Framework & API Vulnerabilities Snoop Doggy Dog

Visiting **http://141.85.224.115:7002/** shows a dog-themed marketing website. Nothing interactive or suspicious on the main page, but… In the Pricing section, there's an unusual plan: ***“Walk your dog with a drone” → Link to dronewalkv125.html***

### Investigating dronewalkv125.html
This page contains a contact form with a Discount Code field and a JavaScript line commented out:

```html
<!-- <script src="form/js/drone_version_check.js"></script>--> <!-- Check discount code, dawg! -->
```

We manually opened: **http://141.85.224.115:7002/form/js/drone_version_check.js** Inside, we found an obfuscated string: **alert("Previous drone walk version: 109");**

Using the version hint, we tried accessing: **http://141.85.224.115:7002/dronewalkv109.html**
It worked. Inside, the Send Email button linked to: **http://141.85.224.115:7002/ya6sb1bfhfyacuyt.html**

Opening **/ya6sb1bfhfyacuyt.html** revealed a picture of four happy running dogs and the flag embedded in it: **SSS{wh0_let_the_d0gs_0ut}**
