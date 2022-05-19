## Challenge Info #
<p>
  The secret vault used by the Longhir's planet council, Kryptos, contains some very sensitive state secrets that Virgil and Ramona are after to prove the injustice performed by the commission. Ulysses performed an initial recon at their request and found a support portal for the vault. Can you take a look if you can infiltrate this system?
</p>

## Solve ##
<li>
  To solve this we need to perform stored XSS attack and intercept password change on moderator account and modify the entry to admin and in the end login as admin to get the flag
 </li>
 
 
 </br>
 
 
 ![image](https://user-images.githubusercontent.com/85706972/169369305-fb375afc-956b-4ebc-ae3d-ef4b32a0f89c.png)


</br>

<li>
  Getting into the challenge we have place for the input and in backend we have login form which at first we tried to perform SQLi, but that did not worked so back to main page...
 </li>
 
 <li>
  Second idea was about stored XSS and to steal the cookie, here is good reference how to do it with webhook - https://infinitelogins.com/2020/10/13/using-cross-site-scripting-xss-to-steal-cookies/ 
  </li>
  
 <li>
  Now we can create malicious XSS payload to steal cookie and wait on webhook site to see if we get any response back
 </li>
 
 </br>
  
![image](https://user-images.githubusercontent.com/85706972/169371126-717a193f-1d0f-422a-8546-82a8696ac7b8.png)

<li>
  When the ticket is sent we should see the cookie in webhook.site portal
</li>
</br>

![image](https://user-images.githubusercontent.com/85706972/169371650-b2db49d8-0bfe-48ff-8370-bdb8b16277be.png)

<li>
  Our task now is to add this cookie to our browser and try to go to location /admin which is the place for admin vault , after refreshing browser we are in, but there is no flag
</li>

</br>

![image](https://user-images.githubusercontent.com/85706972/169371908-02aac37c-623f-4b1a-860c-4148df236bb3.png)

<li>
 Since we got moderator cookie, we need to find way to  login as admin, under settings there is option to change password for moderator user, what if we intercept this request with burp??
</li>

</br>

![image](https://user-images.githubusercontent.com/85706972/169372058-78921af7-333f-4af9-8548-2473a116175a.png)


<li>
 Open Burpsuite and enable foxyproxy and after intercepting the password change attempt we see that uid is set to 100 which is moderator user, usually first user should be admin, so changing the uid = 1 we are able to change the password for admin account
</li>

</br>

![image](https://user-images.githubusercontent.com/85706972/169373567-409a2a53-6f7f-4a3c-8c4c-538f75be448f.png)

</br>

<li>
  Changing the uid = 1 and forward request back to server, we see that admin password is change
</li>

![image](https://user-images.githubusercontent.com/85706972/169373656-45a049d6-49b4-416a-9804-e34729ec3e0e.png)

<li>
  Now we are able to login as admin and grab the flag
 </li>
 
 
 ![image](https://user-images.githubusercontent.com/85706972/169373988-bc58aded-3ca5-46cf-bbca-480da97cc224.png)

