# Super Secure #

## Challenge Info ##
<p>This is so unbreakable!</p>

## Solve ##
<p>Opening this challenge we are presented with soimple login form with submit and reset functions, one of my first guess and tries was to do basic authentication
  with common usernames and password, but that seemed to not work</p>
  
  ![image](https://user-images.githubusercontent.com/85706972/167288827-31c2b442-018f-4ff9-abe3-d90f2e992fb2.png)

<p>Then I went to check source code and noticed that tittle included info about PHP and mysql and then I though maybe it's simple SQL injection</p>

![image](https://user-images.githubusercontent.com/85706972/167288868-b5acd8c0-6813-49f0-99d0-9931a7fee74f.png)

<p>Which it actually was since we simply tried to break the SQL Injection with (') </p>

![image](https://user-images.githubusercontent.com/85706972/167288979-4686ffac-4b66-487e-8c7d-2efdf914ab2d.png)

<p>Now we need to make the statement so that username is true no matter what password we put in and that can be achieved with this form
  username - ' OR 1=1— 
  password - foo

  ![image](https://user-images.githubusercontent.com/85706972/167289044-909d9963-ac78-41b8-9262-c4bb11b5ac48.png)
