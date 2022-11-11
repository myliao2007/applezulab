# 設定 DHCPd server

```
下面這是一些曾經設定過的選項：

* 修改 dhcpd.conf 

* 修改租約期限 
default-lease-time 250000; 
max-lease-time 518000 

* 設定 domain name and dns ip 
option domain-name "domain name"; 
option domain-name-servers 168.95.1.1, 139.175.150.20; 

* 設定分配 IP 
subnet 192.168.1.0 netmask 255.255.255.0 { 
  range 192.168.1.150 192.168.1.180; 
  option broadcast-address 192.168.1.255; 
  option routers 192.168.1.254; 
} 

* 設定靜態固定 IP 
host inside{ 
  hardware ethernet 00:11:22:33:44:55; 
  fixed-address 192.168.1.10; 
  options broadcast-address 192.168.1.255; 
  option routers 192.168.1.254; 
}
```
