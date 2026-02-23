# "Saint John": what is writing to this log file?
---
Description: A developer created a testing program that is continuously writing to a log file /var/log/bad.log and filling up disk. You can check for example with tail -f /var/log/bad.log.
This program is no longer needed. Find it and terminate it. Do not delete the log file.

Test: The log file size doesn't change (within a time interval bigger than the rate of change of the log file).

The "Check My Solution" button runs the script /home/admin/agent/check.sh, which you can see and execute.
---

## Solution

Check what file is writing to **/var/log/bad.log**
`lsof +w /var/log/bad.log`

```console
COMMAND   PID  USER   FD   TYPE DEVICE SIZE/OFF   NODE NAME
badlog.py 592 admin    3w   REG  259,1    57794 265802 /var/log/bad.log
```

Kill process
`sudo kill -9 592`

OR
`kill -9 $(fuser -v /var/log/bad.log)`

## Validation script
```console
$ cat /home/admin/agent/check.sh
#!/usr/bin/bash

log_file="/var/log/bad.log"

if [ -f "$log_file" ]; then
    current_size=$(stat -c %s "$log_file")
    sleep 0.5
    new_size=$(stat -c %s "$log_file")

    if [ "$current_size" -eq "$new_size" ]; then
        echo -n "OK"
    else
        echo -n "NO"
    fi
else
    echo -n "NO"
fi
```
