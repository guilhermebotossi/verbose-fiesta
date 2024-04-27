## Execute John with rockyou Wordlist with the hash inside the file

`john --wordlist=/usr/share/wordlists/rockyou.txt --format=<fmt> <file>`

## zip2john

```
zip2john  secure.zip > zipJohn.txt
john --wordlist=/usr/share/wordlists/rockyou.txt zipJohn.txt
```

## rar2john

```
rar2john secure.rar > rarJohn.txt
john --wordlist=/usr/share/wordlists/rockyou.txt rarJohn.txt
```

## ssh2john

```
ssh2john id_rsa > id_rsa_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa_hash.txt
```