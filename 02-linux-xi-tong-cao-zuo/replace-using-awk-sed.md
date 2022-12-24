# 字串取代，使用 awk & sed

**AWK**

有些實驗數據僅需要某幾個欄位值，用 awk 可以簡單達到目的假設 Shell 環境中的字串 str 內容為1 2 3 4 5 6 7 8 9 10，要取出其中幾個數值，如 2 4 6 8 10

```bash
str="1 2 3 4 5 6 7 8 9 10"
echo ${str} | awk ' { print $2 $4 $6 $8 $10 }; '
```

若是檔案 (file) 中有好幾行的字串，分別將每行的其中幾個數值取出，如取出第 2 4 6 8 10 個數值：

```bash
cat file | awk ' { print $2 $4 $6 $8 $10 }; '
```

或是要調換 1 與 2

```bash
str="1 2 3 4 5 6 7 8 9 10"
echo ${str} | awk ' { print $2 $1 $3 $4 $5 $6 $7 $8 $9 $10 };'
```

**Sed**

因為 tr 似乎只能作一對一的字元轉換，所以找到 sed 的方法來轉換一對多的字串。

將一個 "M" 取代成 "000K"

```bash
echo 1M | sed 's/M/000K/'
```

將字串中的每一個 "M" 取代成 "000K"

```bash
echo '1M and 2M' | sed 's/M/000K/g'
```

將檔案 (input.txt) 中 'old' 字串取代成 'new' 字串

```bash
sed -i -e "s/old/new/g" input.txt
```

Reference:

1. Dale Dougherty & Arnold Robbins, sed & awk, Second Edition, O'Reilly, March 1997.
2. Vivek Gite, Bash Shell: Replace a string with another string in all files using sed and perl -pie,[http://www.cyberciti.biz/faq/unix-linux-replace-string-words-in-many-files/](https://web.archive.org/web/20200416073026/http://www.google.com/url?q=http%3A%2F%2Fwww.cyberciti.biz%2Ffaq%2Funix-linux-replace-string-words-in-many-files%2F\&sa=D\&sntz=1\&usg=AFrqEzf4y6mffOAjpyk1Wr5g2SIwMHPEqA), 2007.
3. monsanbu, [http://www.unix.com/unix-dummies-questions-answers/21866-replace-character-tr.html](https://web.archive.org/web/20200416073026/http://www.google.com/url?q=http%3A%2F%2Fwww.unix.com%2Funix-dummies-questions-answers%2F21866-replace-character-tr.html\&sa=D\&sntz=1\&usg=AFrqEzd-GlyIsusVF4b2zmBuIHB0Y99RJQ), 2005.


