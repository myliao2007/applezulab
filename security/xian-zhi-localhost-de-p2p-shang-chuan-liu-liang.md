# 限制 localhost 的 P2P 上傳流量

I write a shell script in bash today in order to limit the upload bandwidth of P2P software in the localhost. The netfilter framework allows me to set the filter rules by process id. Therefore, we just need to search the process id of P2P software and to limit the bandwidth of the process. However, iptables tutorial \[1] said "pid, sid and command matching is broken in SMP kernels since they use different process lists for each processor". The Linux kernel version is 2.6.24-16 in my box, and the function of --pid-owner is not runable in SMP mode. If you want to use this script to limit the upload bandwidth of P2P in the localhost, you should add the file path of this script to the /etc/crontab to let the script run periodically.\


```shell
#!/bin/bash
#
# You need to install the package - sysvutils


echo "---------------------------------------------------------------------";
echo "* Limit the upload bandwidth of indicated programs in localhost";
echo "* Date: Feb. 21, 2009";
echo "* GNU License";
echo "* Author: Aaron Liao (http://applezulab.netdpi.net)";
echo "---------------------------------------------------------------------";

# environment testing
pidof="/bin/pidof";
iptables="/sbin/iptables";
DEBUG=0;
ret=0;

# set programs you want to limit their upload bandwidth
progs_name=( [0]="amule" [1]="emule");
progs_argv=( [0]=10 [1]=15 );
progs_flag=( [0]=0 [1]=0);
pid_list=([0]="-1" [1]="-1");
declare -a pid_list;

# test pidof
if [ ! -e "$pidof" ]; then
    echo "ERR: Program $pidof not found, you should install the packages - sysutils.";
    exit -1;
fi

# test ipt_owner
modprobe ipt_owner
ret=$?

if [ $ret -ne 0 ]; then
    echo ">> ipt_owner not supported by kernel";
    exit $ret;
fi

# initial
$iptables -D OUTPUT -j P2P
$iptables -N P2P
$iptables -F P2P
$iptables -I OUTPUT -j P2P

# search the process id
for index in $(seq 0 $((${#progs_name[@]} -1)))
do
    if [ $DEBUG -eq 1 ]; then
        echo "DEBUG >> progs_name:progs_argv=${progs_name[$index]}: ${progs_argv[$index]}";
    fi

    pid_list[$index]=`$pidof ${progs_name[$index]}`;
    ret=$?

    if [ $ret -eq 0 ]; then
        echo "FOUND ${progs_name[$index]} pid: ${pid_list[@]}"
        progs_flag[$index]=1;
    else
        progs_flag[$index]=0;
        continue;
    fi

done

ret=0;

# limit the bw
for index in $(seq 0 $((${#progs_name[@]} -1)))
do
    if [ ${progs_flag[$index]} -eq 0 ]; then
        continue;
    fi

    tmp_pid=(${pid_list[$index]});

    for offset in $(seq 0 $((${#tmp_pid[@]} -1)))
    do
        $iptables -I P2P -m owner --pid-owner ${tmp_pid[$offset]} -m limit --limit ${progs_argv[$index]}/second -j ACCEPT;
        $iptables -A P2P -m owner --pid-owner ${tmp_pid[$offset]} -j DROP;
    done

done

exit $ret;
```

\
Reference:\
\[1] iptables tutorial, [http://www.netfilter.org/](https://web.archive.org/web/20200306080009/http://www.netfilter.org/).\
\[2] Mendel Cooper, Advanced Bash-Scripting Guide, [http://tldp.org/LDP/abs/html/](https://web.archive.org/web/20200306080009/http://tldp.org/LDP/abs/html/).
