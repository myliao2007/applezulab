# /proc/sys/net/ipv4/conf/eth\* 目錄中的參數

The file Documentation/networking/ip-sysctl.txt in Linux kernel 2.6.26 source tree \[1] explains that the features of the files in /proc/sys/net/ directory. \
\
Here I list what I would like to understand below:\
\
rp\_filter is used to avoid IP spoofing, my understanding is that it will filter the packets whose source IP address excluses with the eth\* interface.\
\
rp\_filter - BOOLEAN\
1 - do source validation by reversed path, as specified in RFC1812 Recommended option for single homed hosts and stub network routers. Could cause troubles for complicated (not loop free) networks running a slow unreliable protocol (sort of RIP),or using static routes.\
\
0 - No source validation.\
\
conf/all/rp\_filter must also be set to TRUE to do source validation on the interface\
\
Default value is 0. Note that some distributions enable it in startup scripts.\
\
\
arp\_filter - BOOLEAN\
1 - Allows you to have multiple network interfaces on the same subnet, and have the ARPs for each interface be answered based on whether or not the kernel would route a packet from the ARP'd IP out that interface (therefore you must use source\
based routing for this to work). In other words it allows control of which cards (usually 1) will respond to an arp request.\
\
0 - (default) The kernel can respond to arp requests with addresses from other interfaces. This may seem wrong but it usually makes sense, because it increases the chance of successful communication. IP addresses are owned by the complete host on Linux, not by particular interfaces. Only for more complex setups like load-\
balancing, does this behaviour cause problems.\
\
arp\_filter for the interface will be enabled if at least one of conf/{all,interface}/arp\_filter is set to TRUE,it will be disabled otherwise\
\
\
arp\_announce - INTEGER\
Define different restriction levels for announcing the local source IP address from IP packets in ARP requests sent on interface:\
\
0 - (default) Use any local address, configured on any interface\
\
1 - Try to avoid local addresses that are not in the target's subnet for this interface. This mode is useful when target hosts reachable via this interface require the source IP address in ARP requests to be part of their logical network configured on the receiving interface. When we generate the request we will check all our subnets that include the target IP and will preserve the source address if it is from\
such subnet. If there is no such subnet we select source address according to the rules for level 2.\
\
2 - Always use the best local address for this target.\
In this mode we ignore the source address in the IP packet and try to select local address that we prefer for talks with the target host. Such local address is selected by looking for primary IP addresses on all our subnets on the outgoing interface that include the target IP address. If no suitable local address is found we select the first local address we have on the outgoing interface or on all other interfaces,\
with the hope we will receive reply for our request and even sometimes no matter the source IP address we announce.\
\
The max value from conf/{all,interface}/arp\_announce is used.\
\
Increasing the restriction level gives more chance for receiving answer from the resolved target while decreasing the level announces more valid sender's information.\
\
arp\_ignore - INTEGER\
Define different modes for sending replies in response to received ARP requests that resolve local target IP addresses:\
\
0 - (default): reply for any local target IP address, configured on any interface\
1 - reply only if the target IP address is local address configured on the incoming interface\
2 - reply only if the target IP address is local address configured on the incoming interface and both with the sender's IP address are part from same subnet on this interface\
3 - do not reply for local addresses configured with scope host, only resolutions for global and link addresses are replied\
4-7 - reserved\
8 - do not reply for all local addresses\
\
The max value from conf/{all,interface}/arp\_ignore is used when ARP request is received on the {interface}\
\
\
arp\_accept - BOOLEAN\
Define behavior when gratuitous arp replies are received:\
0 - drop gratuitous arp frames\
1 - accept gratuitous arp frames\
\
\
app\_solicit - INTEGER\
The maximum number of probes to send to the user space ARP daemon via netlink before dropping back to multicast probes (see mcast\_solicit). Defaults to 0.\
\
I also found that Documentation/filesystems/proc.txt in linux kernel source code tree explains most of features in /proc/sys/ directory. \
\
Reference:\
\[1] Linux kernel source code, [http://www.kernel.org](https://web.archive.org/web/20200306075510/http://www.kernel.org/).
