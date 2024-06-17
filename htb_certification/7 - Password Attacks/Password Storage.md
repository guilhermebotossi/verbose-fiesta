
# Linux

## /etc/shadow

### Format

```shell-session
root@htb:~# cat /etc/shadow

...SNIP...
htb-student:$y$j9T$3QSBB6CbHEu...SNIP...f8Ms:18955:0:99999:7:::
```


| Content                             | Meaning                 | Obs |
| ----------------------------------- | ----------------------- | --- |
| htb-student:                        | `<username>`:           |     |
| \$y\$j9T$3QSBB6CbHEu...SNIP...f8Ms: | `<encrypted password>`: |     |
| 18955:                              | `<day of last change>`: |     |
| 0:                                  | `<min age>`:            |     |
| 99999:                              | `<max age>`:            |     |
| 7:                                  | `<warning period>`:     |     |
| :                                   | `<inactivity period>`:  |     |
| :<br>                               | `<expiration date>`:    |     |
| :                                   | `<reserved field>`      |     |

#### Password Encryption

Considering the following passoword ==`$y$j9T$3QSBB6CbHEu...SNIP...f8Ms:`==

| `$ <id>` | `$ <salt>` | `$ <hashed>`                  |
| -------- | ---------- | ----------------------------- |
| `$ y`    | `$ j9T`    | `$ 3QSBB6CbHEu...SNIP...f8Ms` |


The type (`<id>`) is the hash algorthm used to encrypt the password. 

|**ID**|**Cryptographic Hash Algorithm**|
|---|---|
|`$1$`|[MD5](https://en.wikipedia.org/wiki/MD5)|
|`$2a$`|[Blowfish](https://en.wikipedia.org/wiki/Blowfish_(cipher))|
|`$5$`|[SHA-256](https://en.wikipedia.org/wiki/SHA-2)|
|`$6$`|[SHA-512](https://en.wikipedia.org/wiki/SHA-2)|
|`$sha1$`|[SHA1crypt](https://en.wikipedia.org/wiki/SHA-1)|
|`$y$`|[Yescrypt](https://github.com/openwall/yescrypt)|
|`$gy$`|[Gost-yescrypt](https://www.openwall.com/lists/yescrypt/2019/06/30/1)|
|`$7$`|[Scrypt](https://en.wikipedia.org/wiki/Scrypt)|
