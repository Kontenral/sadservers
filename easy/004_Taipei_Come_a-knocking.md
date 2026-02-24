# "Taipei": Come a-knocking
---
Description: There is a web server on port :80 protected with Port Knocking. Find the one "knock" needed (sending a SYN to a single port, not a sequence) so you can curl localhost.

Root (sudo) Access: False

Test: Executing curl localhost returns a message with md5sum fe474f8e1c29e9f412ed3b726369ab65. (Note: the resulting md5sum includes the new line terminator: echo $(curl localhost))
---

## Solution

`ls -la`
```console
total 36
drwxr-xr-x 6 admin admin 4096 Feb 24 14:56 .
drwxr-xr-x 3 root  root  4096 Sep 17  2023 ..
drwx------ 3 admin admin 4096 Sep 17  2023 .ansible
-rw-r--r-- 1 admin admin  220 Aug  4  2021 .bash_logout
-rw-r--r-- 1 admin admin 3526 Aug  4  2021 .bashrc
drwxr-xr-x 3 admin admin 4096 Feb 24 14:56 .config
-rw-r--r-- 1 admin admin  807 Aug  4  2021 .profile
drwx------ 2 admin admin 4096 Sep 17  2023 .ssh
drwxr-xr-x 2 admin root  4096 Sep 19  2023 agent
```

`touch script.sh && chmod u+x script.sh && vim script.sh`
```console
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    13  100    13    0     0   4333      0 --:--:-- --:--:-- --:--:--  4333
Who is there?
```
`cat script.sh`
```console
#!/usr/bin/env bash
for port in {1..65535}; do
        knock localhost "$port" 2>/dev/null
done
echo $(curl localhost)
```
```console
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    13  100    13    0     0   4333      0 --:--:-- --:--:-- --:--:--  4333
Who is there?
```

`echo $(curl localhost) | md5sum`
```console
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    13  100    13    0     0  13000      0 --:--:-- --:--:-- --:--:-- 13000
fe474f8e1c29e9f412ed3b726369ab65  -
```


## Validation script
`cat /home/admin/agent/check.sh`
```console
#!/bin/bash
res=$(curl -s localhost)
res=$(echo $res|tr -d '\r')
checksum=$(echo $res| md5sum| awk '{print $1}')

if [[ "$checksum" = "fe474f8e1c29e9f412ed3b726369ab65" ]]
then
  echo -n "OK"
else
  echo -n "NO"
fi
```
