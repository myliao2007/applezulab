# 常用 VIM 選項

```
編輯加密文字檔
$ vim -x filename

基本指令：
Ctrl + [ 等同於 ESC 可切換到指令模式

u 還原改變

ZZ ：直接存檔，然後離開 VIM。

ctrl + R : REDO

J : 合併行數，例如要合併三行: 3J

shift + % : 移到程式區塊所對應的括號

從 VIM 的 manual 學到的一些技巧
:help 可進入閱讀 manual

在主題上，Ctrl + ] 可進入該主題，
Ctrl + o 或 Ctrl + T 可退回 ..

指令：功能

: 4,8t 20
將第4行至第8行複製到第20行

: 4,10 m 15
將第4行至第10行搬到第15行

自動完成拼字
Ctrl+P

專業模式：

V：整行選擇
v：從游標部份開始選
Ctrl + V：可選矩形

切割視窗：

3split 1.c：使用三行的空間，編輯 1.c

CTRL-W h move to the window on the left
CTRL-W j move to the window below
CTRL-W k move to the window above
CTRL-W l move to the window on the right

CTRL-W t move to the TOP window
CTRL-W b move to the BOTTOM window

:qall 關閉所有 VIM 切割的視窗

vim -o one.txt two.txt three.txt
切割成三個視窗來檢視這三個檔案

history: 列出用過的 VIM 指令

於 VIM 中執行 shell 指令：

ex.
: ! ifconfig

編輯二進位檔
vi -b binary_file

設定 UNIX / DOS format
: set display=uhex

選項，可存於 ~/.vimrc

set background=dark
說明：設定背景亮度，可用選項: dark, light

設定 tab 寬度預設 8 個空白寬，改成四個空白寬
set softtabstop=4
set shiftwidth=4

vim 不要嗶嗶聲
set noeb


啟動程式碼語法辨識:
syntax on

語法自動對齊
set cindent
set autoindent

防止貼上亂七八糟
set paste
set nopaste

畫面(不)換行
set nowrap
set wrap

VIM 與 ctags，用 vim 搭配 tags 來看 source code 非常便利，

建立 tags，此處為將所有附檔名為 .c .h 建立 tags
$ find . -name "*.[ch]" | xargs ctags -a

然後使用 vim 看 source code 時可以使用下列指令進行追蹤：
use Ctrl + ] to goto the function body.
use Ctrl + T TO return back where the function called.

我的~/.vimrc 常用設定
set nu
set bg=dark
set hlsearch
set tabstop=8 softtabstop=0 expandtab shiftwidth=4 smarttab
syntax on

Ref:
http://www.study-area.org/tips/vim/index.html
大家來學VIM（一個歷久彌新的編輯器）
Edward Lee
```
