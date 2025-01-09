##### Security Headers.

1. X-Powered-By:
   It tell you which server you are using. and it may be the considered you are exposing what you are using? X-powered-by in headers will tell you which server you are using so in our case
   it will be "express".So use removeHeader to remove that header.

   ```
   app.use((req, res, next)=>{
       res.removeHeader('X-Powered-By');
       next();
   });
   ```

2. Referrer-Policy:
   When we share something (lindn post, yt vedio) from one platform to other then it will brings us where does this traffic coming!! It tracked using referrer policy.
   Suppose we are on flipkart.com
   here we came from google.com so ref policy will say google.com

   example:
   <img width="937" alt="image" src="https://github.com/user-attachments/assets/8bb16480-d34b-4846-b796-4b303e7d0707" />

   But in some cases a lot of information get exposed when you go from instagram to lindn then a lot more info as a form of url will get shared in the referrer policy. So the policy should be strict
   in most of cases it will be "Referrer-policy: strict-origin-when-cross-origin". If may leadking entire uRL when redireting to some other domain so take care of this one!

3. X-Content-Type-Options:
   Considering example, We have client and server and client is aking for Jpg file and sent and request to server, Now hacker or someone in middle as a proxy updated the file type and pushed the malacious code in it and then pass the file to client!!
   earlier Browsers were doing an interesting thing that call sniffing. When response got back to the client the browser use to check what content-type of response you are getting and what is the content. Now depending on the content (suppose hacker changed the file type of jpg to html). 
   Now here browser will consider file as html and it will getting shared to client. So what browser did here browser checked the content and assume the content type as html and Aint checked the content-type which is actually jpg we asked for. So we have this header!


   ```
    app.use((req, res, next)=>{
      res.setHeader('X-Content-Type', 'nosniff');
   });
   
   ```
