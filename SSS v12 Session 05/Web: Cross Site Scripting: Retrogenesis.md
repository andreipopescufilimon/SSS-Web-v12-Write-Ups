# Web: Cross Site Scripting: Retrogenesis

Find the flag at ctf-05.

Problem: first of all we go to inspect element and then type **alert(1)** in the console, javascript for **</script>alert(1)</script>** then the site returns the value of 1.
So then we can proceed to getting the cookies from the site, so in the console we type the javascript to get the cookies: 

```
const allCookies = document.cookie;
console.log(allCookies);
```

Then we can observe the cookies transmitted to us are: 
```
csrftoken=wF2xNkcgTKSKuKOYFWOSeKeGqOvwSEVttvIgvR0tXRmRvuIHdMzUg3n7AvJfChdB; 
flag="SSS{retr0_music_for_the_masses}"
```

So, the flag is: **SSS{retr0_music_for_the_masses}**.
