# 透過 RS-232 操作 Linux console



Debian sample

修改 : /etc/lilo.conf

增加 2 行 serial=0,9600n8 vga = normal #

image= ... append="console=ttyS0"

改好之後重新執行 lilo

修改 : /etc/inittab

\#Example how to put a getty on a serial line (for a terminal)

\#T0 for ttyS0

T0:23:respawn:/sbin/getty -L ttyS0 9600 vt100 #T1:23:respawn:/sbin/getty -L ttyS1 9600 vt100

or

/usr/doc/faq/howto/NET-2-HOWTO.gz 有講得很清楚.

1. /etc/inittab 在 # Serial lines 這段開一個 s2:45:respawn:/sbin/agetty -mt60 38400 ttyS1
2. /etc/rc.d/rc.S 最下一列開 /bin/sh /etc/rc.d/rc.serial
3. 如果您的 PC 沒有 multi-port of serial 的話, rc.serial 可以不要管了, 不過可以再去 check 一下, 我都是把 # Do AUTOMATIC\_IRQ probing 這段的 #AUTO\_IRQ=auto\_irq 關掉. 去 # MANUAL CONFIGURATION 把 ${SETSERIAL} /dev/cua0 uart 16550 port 0x3F8 irq 4 ${SETSERIAL} /dev/cua1 uart 16550 port 0x2F8 irq 3 打開
4. /etc/default 加一個 agetty.ttyS1
