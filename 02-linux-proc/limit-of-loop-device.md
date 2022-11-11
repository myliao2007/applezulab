# loop device 的數目

Linux kernel 預設的 loop device 數目最多只可以同時使用 8 個 loop devices,

因此可以修改核心 $src/linux/drives/block/loop.c

將 max\_loop 的值，由 8 改為所需要的值，範圍在1 \~ 255

並且重新編譯核心即可。
