# 實驗基本數據製圖指令 gnuplot



* 開啟程式：gnuplot
* 開啟資料檔案：plot "filename"
* 設定要取的資料欄位，ex. 取第四、第五欄：plot 'file' index 4:5
* 開啟多個資料檔案：plot "file-1", "file-2", "file-3"
* 開啟多個資料檔案指定風格：plot "file-1" with points, "file-2" with line, "file-3" with linespoints
* 設定線條說明在左上方：set key tmargin left
* 設定線條說明有外框：set key box
* 設定數據連成一條線：set style data lines
* 不要線條說明：set nokey
* 啟動網格：set grid
* 設定標題：set title
* 設定 x 軸 Label：set xlabel "x\_description (unit)"
* 設定 y 軸 Label：set ylabel "y\_description (unit)"
* 設定 x 軸值範圍 0\~300：set xrange \[ 0 : 300 ]
* 設定 y 軸值範圍 0\~100：set yrange \[ 0 : 100 ]
* 設定 x 軸由 0 開始：set xrange \[ 0: ]
* 呈現圖式：plot
* plot "filename" with boxes
* 清除設定：reset
* 解除設定：unset command (ex. unset title)
* 儲存設定：save "set.gp"
* 載入設定：load "set.gp"
* 輸出 png 圖檔：( others: jpeg, gif, pdf, ...)
* set output "filename.png"
* set terminal png
* replot
* 畫完圖檔若是要重新將圖改回在螢幕呈現
* Linux: set terminal x11
* Windows: set terminal win
* 秀出所有設定：show all
* 資料呈現風格：
* 點與線：plot "filename" with linespoints
* 點：plot "filename" with points
* 線：plot "filename" with line

Reference:

1. 中央研究院計算中心, Gnuplot 導讀,[http://phi.sinica.edu.tw/tyuan/old.pages/pcfarm.19991228/aspac/aspac/reports/95/95006/.](http://www.google.com/url?q=http%3A%2F%2Fphi.sinica.edu.tw%2Ftyuan%2Fold.pages%2Fpcfarm.19991228%2Faspac%2Faspac%2Freports%2F95%2F95006%2F\&sa=D\&sntz=1\&usg=AFrqEzf7qI2zDgiqpDyMZlztBv\_PrROW4Q)
2. [http://t16web.lanl.gov/Kawano/gnuplot/](http://www.google.com/url?q=http%3A%2F%2Ft16web.lanl.gov%2FKawano%2Fgnuplot%2F\&sa=D\&sntz=1\&usg=AFrqEzcwfjr3GhgnmGZGfxStHK8BAdQ4fQ).
3. GNUplog homepage, [http://www.gnuplot.info/](http://www.google.com/url?q=http%3A%2F%2Fwww.gnuplot.info%2F\&sa=D\&sntz=1\&usg=AFrqEzebnW15oPvDXsMwgsivgDrzdsb8nA).
