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

