# Linux connect to serial port



```bash
minicom -D /dev/ttyS0
socat stdin,raw,echo=0 /dev/ttyUSB0,raw,echo=0
```
