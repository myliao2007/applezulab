# 偵測 NAT 內部主機連線狀況

偵測 NAT 內部主機連線狀況，如果斷線，E-mail 通知 admin

```shell
#!/bin/bash

## need to install postfix first

DEBUG=0

echo "120" > /proc/sys/net/ipv4/netfilter/
ip_conntrack_generic_timeout


LOG="/proc/net/ip_conntrack"
SERV_IP="60.199.220.6"
MSG_FROM="140.116.177.231"
CLI_IP1="192.168.50.37"
CLI_IP2="192.168.50.38"
det1=0
det2=0
f_old_det1="/tmp/old_det1"
f_old_det2="/tmp/old_det2"
mail_content="/tmp/mail"
admin="aaron@netdpi.net"


touch $f_old_det1
touch $f_old_det2

old_det1=`cat $f_old_det1`
old_det2=`cat $f_old_det2`

det1=`cat $LOG|grep $SERV_IP|grep $CLI_IP1|grep ESTABLISH|wc -l`
det2=`cat $LOG|grep $SERV_IP|grep $CLI_IP2|grep ESTABLISH|wc -l`

if [ $DEBUG -eq 1 ]; then

       echo "old_det1: $old_det1"
       echo "old_det2: $old_det2"
       echo "det1: $det1"
       echo "det2: $det2"

fi

echo $det1 > $f_old_det1
echo $det2 > $f_old_det2


cat /dev/null > $mail_content

echo "MSG FROM: $MSG_FROM" >> $mail_content
echo -n "Detection date: " >> $mail_content
echo
date >> $mail_content


if [ $det1 -lt 2 ]; then

       echo "$CLI_IP1 disconnect!" >> $mail_content

else
       echo "$CLI_IP1 connect!" >> $mail_content

fi

if [ $det2 -lt 2 ]; then

       echo "$CLI_IP2 disconnect!" >> $mail_content

else

       echo "$CLI_IP2 connect!" >> $mail_content

fi

MAIL_FLAG=0

if [ "$det1" != "$old_det1" ]; then

       MAIL_FLAG=1
fi

if [ "$det2" != "$old_det2" ]; then

       MAIL_FLAG=1
fi

if [ $MAIL_FLAG -ne 0 ]; then

       mail -s "[note] JS Detection `date +%y/%m/%d-%H:%M`" $admin < $mail_content

fi

exit 0;shell
```



