# Passman

>Pandora discovered the presence of a mole within the ministry. To proceed with caution, she must obtain the master control password for the ministry, which is stored in a password manager. Can you hack into the password manager?

# Solve

In this challenge we also are presented with login page, but with extra functionality to create new user, before doing that I like to use burp and catch all traffic through it for later inspection if I miss something important...

![image](https://user-images.githubusercontent.com/85706972/227312286-07f8a872-3d70-4f6c-b500-387507ff30d3.png)

Creating new user and looking into the the burp we see the HTTP body field with required data and response when user created 

![image](https://user-images.githubusercontent.com/85706972/227312770-97c1b245-b18c-4e1c-8260-6fc98e1a4695.png)

When we login as our newly created user we password manager options to create for email, web, and other which all I tried and did not found nothing interesting

![image](https://user-images.githubusercontent.com/85706972/227313274-7c5a0fd3-488c-43c4-9c55-bb752746dd8b.png)

Then I tried to look into provided source code and noticed function <strong>UpdatePassword</strong>, but I did not see any buttons or place where I could possibly do that on the GUI interface

![image](https://user-images.githubusercontent.com/85706972/227313530-1bd9d645-cdf2-473f-8ea4-de8ba658ffa9.png)

Then I went back to burp and started to check all the requests I done and compared to the function in source code and nothing really stood out, until the moment I started to think like hacker and tried myself to change one of the functions and tried to change the password for my user

![image](https://user-images.githubusercontent.com/85706972/227313963-100d0bea-a5e3-4882-b915-cc833fa2c1c8.png)

Which worked just fine, and then I thougth why just we can't try to change the password for admin user? Well, it worked out also, it looks like we found IDOR vulnerability...

![image](https://user-images.githubusercontent.com/85706972/227314217-814b7d22-6dd1-4ed2-8bb1-f9c4aa9d2278.png)

Using changed credentials we were able to login as admin and retrieve the flag 

![image](https://user-images.githubusercontent.com/85706972/227314419-696b0048-3e6f-4a26-85be-db838ee22dd3.png)





