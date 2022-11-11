# Remote Control over CGI

Requirement: miniserv \[1] (lightweight web server) and bashlib \[2]

```shell
#!/bin/bash

. /usr/local/bin/bashlib
DIP=`param ip`
ODPORT=`param odport`
IDPORT=`param idport`
echo "<pre>"
if [ "$DIP" == "" ]; then
  echo "no IP"
  exit
fi
if [ "$ODPORT" == "" ]; then
  echo "no ODPORT"
  exit
fi
if [ "$IDPORT" == "" ]; then
  echo "no ODPORT"
  exit
fi

REMOTE_ADDR=`echo $REMOTE_ADDR|sed s'/[^0-9.]\{1,15\}//g'`
ODPORT=`echo $ODPORT|sed s'/[^0-9]\{1,5\}//g'`
DIP=`echo $DIP|sed s'/[^0-9.]\{1,15\}//g'`
IDPORT=`echo $IDPORT|sed s'/[^0-9]\{1,5\}//g'`

CMD="/sbin/iptables -t nat -I DDNAT -p tcp -s $REMOTE_ADDR --dport $ODPORT -j DNAT --to $DIP:$IDPORT"
echo "$CMD"|grep "rm"
if [ $? -eq 0 ]; then
  echo "illegal instruction: $CMD"
fi
echo $CMD
`$CMD`
echo "</pre>"
#setll
```

1. miniserv, [http://sourceforge.net/projects/miniserv/](https://web.archive.org/web/20200306075435/http://sourceforge.net/projects/miniserv/)
2. bashlib project, [http://bashlib.sourceforge.net/](https://web.archive.org/web/20200306075435/http://bashlib.sourceforge.net/).
