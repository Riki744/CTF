# I made a blog! #

## Challenge Info ##
<p>Did you check out my blog posts?</p>

## Solve ## 
<p>Website of the challenge was looking like a simple blog site and at first I went over the site to see what functions are implemented and working so we can try to find the place for vulnerability which stands out</p>

![image](https://user-images.githubusercontent.com/85706972/167289143-e958f2f3-afbb-4584-a872-c95a04f6dbe1.png)

<p>In one of the blog posts I noticed that URL changed by adding (?file=) parameter and this let me try to test it for LFI vulnerability by just looking at 
  /etc/passwd file</p>
  
  ![image](https://user-images.githubusercontent.com/85706972/167289194-f5957418-5456-4b5d-87d9-6fc8571c4473.png)
  
  <p>At this point I started to think about my options what I can possibly to do, so we can get the flag, one of my ideas was RFI to upload file and maybe get
  reverse shell, but for that we need to be on same network or public network and this is not going to work, second idea was about log poisoning, but I couldn't 
  open any directory that would let me do poisoning, then I searched a little bit and used php://filter option to convert file to base64 and we can possibly then read the logs or any other file that we have access
  
![image](https://user-images.githubusercontent.com/85706972/167289328-663e9783-792b-4f18-82d4-f51dfe53fadb.png)

<p>Only file that I'd access was the vulnerable BlogConfig.php file and by decoding base64, we only saw actuall code that was vulnerable to LFI </p>

![image](https://user-images.githubusercontent.com/85706972/167289360-77c8b2ab-b7b5-4250-9efe-fa6b2694d7c8.png)

<p>From this point I wasn't sure what to do, so I started from beginning and tried to check robots.txt</p>

![image](https://user-images.githubusercontent.com/85706972/167289398-3dc9ba63-826d-444d-9bd3-035c92220369.png)

<p>There we found disallowed entry for flag.php which had hint about filter option to retrieve the flag, but I still wasn't sure what should we look for,
  after couple some time thinking I tried to open the file flag.php and went to decode it </p>
  
  ![image](https://user-images.githubusercontent.com/85706972/167289484-1f3264ad-5783-4e14-be74-ed304be89a3d.png)

<p>In the end it the flag was just commented out, this was the only challenge were I spent that much time</p>

Flag - EZ-CTF{LFI_1S_3Z}
  
