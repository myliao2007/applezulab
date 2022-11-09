# Script:  strncmp

Aaron Liao, 2022

Write a strncmp-like fuction with shell script.

```shell
#!/bin/bash

function _strncmp
{
    local str1=${1}
    local str2=${2}
    local i=0

   while [ ${i} -lt ${#str2} ];
    do
        if [ "${str1:i:1}" != "${str2:i:1}" ]; then
            return 1;
        fi

       i=$(expr ${i} + 1)
    done

   return 0;
}

_strncmp "hello world" "hello"

if [ $? -eq 0 ]; then
        echo "matched"
else
        echo "mismatched"

fi
```
