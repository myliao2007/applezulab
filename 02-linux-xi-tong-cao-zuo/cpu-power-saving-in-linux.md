# 自動 CPU 調頻節能控制

Ubuntu 10.04 以後的系統可以安裝 powernowd，(開機會自動執行 deamon)

週期的監控 CPU 使用率，可以設定上限值與下限，依據設定的 policy 自動調降/調升 CPU 頻率。

Ubuntu Linux 10.04 設定檔 ( /etc/default/powernowd )

man powernowd for details.

手動CPU 調頻控制

測試：&#x20;

Lenovo Notebook X200, CPU (Pentium M) 支援四種頻率 ( 2401, 2400, 1600, 800 MHz)&#x20;

CPU: Intel(R) Core(TM)2 Duo CPU P8600 @ 2.40GHz&#x20;

OS: Ubuntu 10.04 TLS (Linux kernel: 2.6.32-26-generic SMP)

Script code:

```shell
#!/bin/bash

# Lenovo x200 support: 800 MHz, 1600 MHz, 2400 MHz and 2401 MHz
# 可參考 /sys/devices/system/cpu/cpu$cpu/cpufreq/scaling_available_frequencies

num=$#
if [ $num -lt 2 ]; then
        echo "$0 cpu_id cpu_freq"
        exit 1;
fi

cpu="$1"
cpufreq="$2""000"

echo "Set CPU$cpu with $2 MHz"

# 可參考 /sys/devices/system/cpu/cpu$cpu/cpufreq/scaling_available_governors

echo "userspace" > /sys/devices/system/cpu/cpu$cpu/cpufreq/scaling_governor

# 設定 CPU 頻率下限
echo "800000" > /sys/devices/system/cpu/cpu$cpu/cpufreq/scaling_min_freq

# 設定 CPU 頻率上限
echo "2401000" > /sys/devices/system/cpu/cpu$cpu/cpufreq/scaling_max_freq

# 設定 CPU 頻率
echo "$cpufreq" > /sys/devices/system/cpu/cpu$cpu/cpufreq/scaling_setspeed

# 確認設定
echo "cat /sys/devices/system/cpu/cpu$cpu/cpufreq/scaling_setspeed"
out=`cat /sys/devices/system/cpu/cpu$cpu/cpufreq/scaling_setspeed`
echo $out

cat /proc/cpuinfo |grep "cpu MHz"

exit 0;

```

```
使用方式：
1. 將上述 script 存為 set_cpu.sh 
2. 變更權限：chmod +x set_cpu.sh
3. ./set_cpu.sh cpu_id cpu_freq   ( 將 cpu_id 改成 /proc/cpuinfo 對應的 processor id, cpufreq 改成要設定的頻率, ex. ./set_cpu.sh 0 800 )

註：如果要手動調整頻率，需要移除下列自動調頻的工具
$ sudo apt-get remove powernowd cpudyn cpufreqd powersaved speedfreqd

 
一些資訊：
driver: acpi-cpufreq

/sys/devices/system/cpu/cpu0/cpufreq
/sys/devices/system/cpu/cpufreq/ondemand (decides the speed to use)

cpufreq-info 可以獲得 cpufreq stats (有統計每個頻率使用的百分比)

/proc/acpi 可以調整亮度, 查詢 battery 資訊

set ex. echo "70" > /proc/acpi/video/VID/LCD0/brightness

/proc/acpi/ac_adapter/AC  (可查詢是否插電)

/proc/acpi/ibm 可控制 ibm devices

2.6.26 (mp)

/sys/devices/system/cpu/cpu? 中,

設定開啟、關閉 CPU: online { 1, 0 }

/sys/devices/system/cpu/sched_smt_power_savings

   0 - No power saving load balance (default value)
   1 - Fill one thread/core/package first for long running threads
   2 - Also bias task wakeups to semi-idle cpu package for power savings

/sys/devices/system/cpu/kernel_max

   kernel_max: the maximum cpu index allowed by the kernel configuration.

   offline: cpus that are not online because they have been  HOTPLUGGED off
   or exceed the limit of cpus allowed by the kernel configuration
   (kernel_max above).


   online: cpus that are online and being scheduled.

   possible: cpus that have been allocated resources and can be
             brought online if they are present.

   present: cpus that have been identified as being present in the system.

需要更多資訊可直接查看 Reference 所列資料。

 
 
Reference: (我所查看的程式碼版本是 Linux kernel source 2.6.32, 2.6.36)

Documentation/cputopology.txt for more information.
Documentation/cpuidle/.
Documentation/ABI/testing/sysfs-devices-system-cpu.
http://www.pantz.org/software/cpufreq/usingcpufreqonlinux.html 
```



