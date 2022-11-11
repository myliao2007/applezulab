# 製作 patch 檔

例如：

old.c 是舊的程式原始碼，new.c是修改過的新程式碼，

只要使用 diff 就能夠製作出patch檔

diff old.c new.c -Naur > patch.sample

拿到 patch 檔的人只要下這行指令，便可以將他手中的old.c 更新為與 new.c 一樣的程式碼。

patch -p0 < patch.sample 如果目錄中有多個原始檔，也可以整個目錄 diff # diff DirA DirB -Naur > patch.sample
