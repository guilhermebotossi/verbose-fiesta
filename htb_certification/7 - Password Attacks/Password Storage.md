
# Linux

## /etc/shadow

### Format

```shell-session
root@htb:~# cat /etc/shadow

...SNIP...
htb-student:$y$j9T$3QSBB6CbHEu...SNIP...f8Ms:18955:0:99999:7:::
```


| htb-student:  | $y$j9T$3QSBB6CbHEu...SNIP...f8Ms: | 18955:                  | 0:           | 99999:       | 7:                  | :                      | :                    | :                  |
| ------------- | --------------------------------- | ----------------------- | ------------ | ------------ | ------------------- | ---------------------- | -------------------- | ------------------ |
| `<username>`: | `<encrypted password>`:           | `<day of last change>`: | `<min age>`: | `<max age>`: | `<warning period>`: | `<inactivity period>`: | `<expiration date>`: | `<reserved field>` |
