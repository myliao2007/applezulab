# Linux 設定網卡速度

透過限制 advertise，可以限制 autoneg 後的網卡傳輸速度:\
\
ex. ethtool -s eth0 advertise 0x008\
\
並使用 ethtool eth0 查看網卡目前模式的速度\


> advertise 數值的意義:\
> 0x001 10 Half\
> 0x002 10 Full\
> 0x004 100 Half\
> 0x008 100 Full\
> 0x010 1000 Half\
> 0x020 1000 Full\
> 0x8000 100000 Full\
> 0x03F Auto

\
Reference:\
1\. man ethtool.
