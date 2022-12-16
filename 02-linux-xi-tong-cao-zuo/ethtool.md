# 以 ethtool 查看網路卡狀態及設定

執行 ethtool 需要有 root 權限（以 root 或 sudo 執行）

顯示、設定 Ring buffer

```bash
ethtool -g ethX
ethtool -G ethX
```

\
顯示網路卡統計資訊

```bash
ethtool -S ethX
```

\
顯示 offload 功能

```bash
ethtool -K ethX
```

透過限制 advertise，可以限制 autoneg 後的網卡傳輸速度，可使用 ethtool eth0 查看網卡目前模式的速度。

```bash
ethtool -s eth0 advertise 0x008
```

\
advertise 數值的意義:\
0x001 10 Half\
0x002 10 Full\
0x004 100 Half\
0x008 100 Full\
0x010 1000 Half\
0x020 1000 Full\
0x8000 100000 Full\
0x03F Auto\\

\
更多資料請參考 ethtool 使用手冊 (man ethtool)
