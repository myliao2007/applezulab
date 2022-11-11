# 在 Linux 系統呈現 dd 的進度

範例：定時呈現 dd 進度\
\
撰寫一個名為 show\_all\_dd.sh 的 shell script, 針對全部的 dd 指令：\


```shell
#!/bin/bash
while 
  killall -USR1 dd;
  do sleep 5; 
done
```

或者用 pid 指定特定的 dd process，假設將 shell 存為 show\_dd.sh 檔名，\
\
可用 ./show\_dd.sh pid 來執行［pid 是 dd 的 process ID］\


```shell
#!/bin/bash
# 這邊要依據自己系統上的 dd process ID 
pid=$1

while 
  kill -USR1 $pid;
  do sleep 5; 
done
```

\
參考資料\
晟誼科技，[http://www.datasos.tw/jiuyuanjishu/20130805/2300.html](https://web.archive.org/web/20200306075605/http://www.datasos.tw/jiuyuanjishu/20130805/2300.html)
