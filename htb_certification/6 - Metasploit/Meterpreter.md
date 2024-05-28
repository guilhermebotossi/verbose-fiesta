
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
```

## Find the existing exploit in MSF and use it to get a shell on the target. What is the username of the user you obtained a shell with?



## Retrieve the NTLM password hash for the "htb-student" user. Submit the hash as the answer.

