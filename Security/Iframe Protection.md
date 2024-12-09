### Iframe Protection.

There are some security concerns tho...Lets check!!!

Suppose you have a website a.com and we embedded some ad or something from outside domain like b.com or ads.com. Check below example:

<img width="293" alt="image" src="https://github.com/user-attachments/assets/0bc2673a-26b0-44bd-978f-0b22e1939dec">

What are the things are may go wrong:
What if b.com can able to access the a.com's resources then it will be critical..!!! or it can be able to temport the content of a.com then also it will be a security breach. To ensure that they will be working with isolation we need to take care of a lot of things.

vulnarabilities:
1. Click Hijacking: (consider have any application and have option show more or show details, What if you have a hidden website which is embedded there exactly at button show more or show details. So when you are clicking on actual application;s button you ended up clicking on that hidden button or hidden button. (It may be a hidden iframe and it causes security breach).
2. Data Theft (Via Js someone can acess your DOM);
3. Session and Cookie Theft (If I can access your session or cookies then also it's security constrained);


Let's try some logic. We'll be having server 1 and server 2. Server 1 is parent and 2 will be a child window or client.

#### Server1:

```js

import e from "express";
const app= new e();
app.get('/server1', function(req,res){
    console.log(res);
   res.sendFile(import.meta.dirname+'/index.html');
})

app.listen(3001, function(){
    console.log('server started');
})

```
The file which server 1 will serve:

```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1>Hello this is Application form</h1>
    <iframe  src="http://localhost:3002/server2"></iframe>
    <button style="color:red">Submit</button>
</body>
</html>

```
Look we embedded the server2's response in i frame:

#### Server2:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1>Hello this is Application form</h1>
    <button style="color:red">Click Here</button>
</body>
</html>
```

```js

import e from "express";

const app= new e();

app.get('/server2', function(req,res){
    console.log(res);
   res.sendFile(import.meta.dirname+'/index.html');
})

app.listen(3002, function(){
    console.log('server started');
})
```

 Now start both of the servers and lets check how does it look???

 <img width="798" alt="image" src="https://github.com/user-attachments/assets/0ce86cec-4cf9-4449-bbb6-78a06ced8b1f">

 Now suppose I faded up the server2's response and exactly mapped the click here button over the submit button!!! So when user tried to perform or submit the application form they will ended up clicking on click here button which will potentially run some malicious code!!! 

 I am too bad with css so Try combining those two HTML and keep the servr2's repose opacity to zero so overlay will not show any content of click here page. And this is the click hijacking!!!

<hr>




