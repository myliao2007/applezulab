# Linux connect to serial port

在 Linux 系統透過 serial port 與裝置連線，e.g. 開發版、router

```bash
minicom -D /dev/ttyS0
socat stdin,raw,echo=0 /dev/ttyUSB0,raw,echo=0
```
