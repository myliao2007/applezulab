# 關於 Linux CPU smp\_affinity

Aaron Liao, 2014\
Linux kernel 中，對於 SMP (multi-core) 內建的支援功能

&#x20; \
Linux Kernel Documentation::IRQ.txt

| <p><strong>What is an IRQ?</strong>　An IRQ is an interrupt request from a device. Currently they can come in over a pin, or over a packet. Several devices may be connected to the same pin thus sharing an IRQ.　An IRQ number is a kernel identifier used to talk about a hardware interrupt source.  Typically this is an index into the global irq_desc array, but except for what Linux/interrupt.h implements the details are architecture specific.  An IRQ number is an enumeration of the possible interrupt sources on a machine.  Typically what is enumerated is the number of input pins on all of the interrupt controller in the system.  In the case of ISA what is enumerated are the 16 input pins on the two i8259 interrupt controllers.<br>   Architectures can assign additional meaning to the IRQ numbers, and are encouraged to in the case  where there is any manual configuration of the hardware involved.  The ISA IRQs are a classic example of assigning this kind of additional meaning.</p> |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

Linux Kernel Documentation::IRQ-affinity.txt

| <p> ChangeLog:<br>        Started by Ingo Molnar &#x3C;<a href="https://web.archive.org/web/20200306075219/mailto:mingo@redhat.com">mingo@redhat.com</a>><br>        Update by Max Krasnyansky &#x3C;<a href="https://web.archive.org/web/20200306075219/https://www.blogger.com/null">maxk@qualcomm.com></a><br><br><strong>SMP IRQ affinity</strong><br><br>/proc/irq/IRQ#/smp_affinity specifies which target CPUs are permitted for a given IRQ source. It's a bitmask of allowed CPUs. It's not allowed to turn off all CPUs, and if an IRQ controller does not support IRQ affinity then the value will not change from the default 0xffffffff.<br>/proc/irq/default_smp_affinity specifies default affinity mask that applies to all non-active IRQs. Once IRQ is allocated/activated its affinity bitmask will be set to the default mask. It can then be changed as described above. <br><br><br>Default mask is 0xffffffff.<br><br>Here is an example of restricting IRQ44 (eth1) to CPU0-3 then restricting<br>it to CPU4-7 (this is an 8-CPU SMP box):<br><br><br><strong>(Note: device 對應的 IRQ number 可以從 /proc/interrupt 查詢)</strong><br><br>[root@moon 44]# cd /proc/irq/44<br>[root@moon 44]# cat smp_affinity<br>ffffffff<br><br>[root@moon 44]# echo 0f > smp_affinity<br>[root@moon 44]# cat smp_affinity<br>0000000f<br>[root@moon 44]# ping -f h<br>PING hell (195.4.7.3): 56 data bytes<br> ...<br><br>--- hell ping statistics ---<br>6029 packets transmitted, 6027 packets received, 0% packet loss<br>round-trip min/avg/max = 0.1/0.1/0.4 ms<br>[root@moon 44]# cat /proc/interrupts | grep 'CPU\|44:'<br>           CPU0       CPU1       CPU2       CPU3      CPU4       CPU5        CPU6       CPU7<br> 44:       1068       1785       1785       1783         0          0           0         0    IO-APIC-level  eth1<br><br>As can be seen from the line above IRQ44 was delivered only to the first four<br>processors (0-3).<br>Now lets restrict that IRQ to CPU(4-7).<br>[root@moon 44]# echo f0 > smp_affinity<br><br>[root@moon 44]# cat smp_affinity000000f0  (f0 -> 11110000, 啟動 5, 6, 7, 8 bit, mapping CPU4, CPU5, CPU6, CPU7)<br><br>[root@moon 44]# ping -f h<br>PING hell (195.4.7.3): 56 data bytes<br> ..<br>--- hell ping statistics ---<br>2779 packets transmitted, 2777 packets received, 0% packet loss<br>round-trip min/avg/max = 0.1/0.5/585.4 ms<br>[root@moon 44]# cat /proc/interrupts |  'CPU\|44:'           CPU0       CPU1       CPU2       CPU3      CPU4       CPU5        CPU6       CPU7 <br>44:       1068       1785       1785       1783      1784       1069        1070       1069   IO-APIC-level  eth1<br><br>This time around IRQ44 was delivered only to the last four processors.<br>i.e counters for the CPU0-3 did not change.</p> |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

Issues:irqbalance 會變動 affinity 的值 (Ref. [http://www.linuxquestions.org/questions/fedora-35/irq-smp\_affinity-439351/](https://web.archive.org/web/20200306075219/http://www.google.com/url?q=http%3A%2F%2Fwww.linuxquestions.org%2Fquestions%2Ffedora-35%2Firq-smp\_affinity-439351%2F\&sa=D\&sntz=1\&usg=AFrqEzeyWz8KDbLGj-mPxrKCfkleiWa6Nw))\
\
Reference: \
深度剖析 irqbalance： [http://blog.yufeng.info/archives/2422](https://web.archive.org/web/20200306075219/http://blog.yufeng.info/archives/2422)\
SMP IRQ Affinity：[https://cs.uwaterloo.ca/\~brecht/servers/apic/SMP-affinity.txt](https://web.archive.org/web/20200306075219/https://cs.uwaterloo.ca/\~brecht/servers/apic/SMP-affinity.txt)\
\
**API**

getcpu(2)\
sched\_setaffinity(2)\
gettid(2)\
CPU\_SET(3)\
sched\_getcpu(3)\
sched\_setaffinity(2)\
sched\_getaffinity(2)\
sched\_setscheduler(2)\
mbind(2)\
set\_mempolicy(2)\
cpuset(7)\
pthread\_self(3) returns the thread ID of the calling thread. \
setpriority(2)\
getpriority(2)\
\
Reference: glibc-doc\
\
**Multi-Core & multi-queue in Linux Kernel**\
\
2.6.35 以後，Linux kernel 已經能夠支援網路封包接收的 multi-queue & multi-core 的能力，\
\
傳送的部份 (XPS) 預計將放入 Linux Kernel 2.6.37 中。\
\
By Packet ( RPS, Receive Packet Steering) & by Flow ( RFS, Receive Flow Steering)

* RPS (Receive Packet Steering): [http://lwn.net/Articles/361440/](https://web.archive.org/web/20200306075219/http://www.google.com/url?q=http%3A%2F%2Flwn.net%2FArticles%2F361440%2F\&sa=D\&sntz=1\&usg=AFrqEzen3nQq1JReY3h\_VTVK\_tXbwQb5NA)
* RFS (Receive Flow Steering): [http://lwn.net/Articles/381955/](https://web.archive.org/web/20200306075219/http://www.google.com/url?q=http%3A%2F%2Flwn.net%2FArticles%2F381955%2F\&sa=D\&sntz=1\&usg=AFrqEzcmCLbOzBRpYcYRU9vleoznORhiEA)
* XPS (Transmit Packet Steering): [http://lwn.net/Articles/416646/](https://web.archive.org/web/20200306075219/http://www.google.com/url?q=http%3A%2F%2Flwn.net%2FArticles%2F416646%2F\&sa=D\&sntz=1\&usg=AFrqEzc4wq1bfYmSm0rOKU6mmTronrPu9w)

\
API 可參考 glibc manual：[http://www.gnu.org/software/libc/manual/html\_node/CPU-Affinity.html](https://web.archive.org/web/20200306075219/http://www.gnu.org/software/libc/manual/html\_node/CPU-Affinity.html)
