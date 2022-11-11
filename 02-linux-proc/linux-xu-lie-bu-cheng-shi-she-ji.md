# Linux 序列埠程式設計

[Aaron Liao](https://web.archive.org/web/20200306075410/http://applezulab.netdpi.net/)\
\
這裡我們探討 Linux 序列埠程式設計，你需要熟悉 [C 語言](https://web.archive.org/web/20200306075410/http://www.books.com.tw/exep/assp.php/vip--af000078707/exep/prod/booksfile.php?item=0010535305)和 Linux。\
\
首先，用下列的程式來開啟序列埠。

```c
int open_port(const char *pathname) {

    /* open the pathname, ex. pathname="/dev/ttyACM0" */ 

    int serial_fd = open(pathname, O_RDWR|O_NOCTTY|O_NDELAY);

    /* fail to open the serial port */
    if(serial_fd<0) {

        if( debug ) {
            printf("%s: fail to open [%s].\n", __func__, pathname);
        }
        return -1;
    }

    printf("%s: %s, serial_fd: %3d\n", __func__, pathname, serial_fd);

    // enable the nonblocking mode for reading.
    fcntl(serial_fd, F_SETFL, FNDELAY);

    return serial_fd;
}
```

接著透過 file descriptor（檔案描述子）設定序列埠。

```c
    #include <termios.h> /* more baud rates could be found here */
    #include <fcntl.h>

    struct termios options;
    speed_t baud = B38400;

    tcgetattr(serial_fd, &options); /* Get the original setting */

    /* Setting the baudrate */
    cfsetispeed(&options, baud);
    cfsetospeed(&options, baud);

    /* enable the receiver and set local mode */
    options.c_cflag |= (CLOCAL|CREAD);

    /* setting the character size (8-bits) */
    options.c_cflag &= ~CSIZE;
    options.c_cflag |= CS8;

    /* Setting Parity Checking: NONE (NO Parity) */
    options.c_cflag &= ~PARENB;
    options.c_cflag &= ~CSTOPB;

    /* Disable Hardware flow control */
    options.c_cflag &= ~CRTSCTS;

    /* Disable Input Parity Checking */
    options.c_iflag &= ~INPCK;

    /* Disable software flow control */
    options.c_iflag &= ~(IXON|IXOFF|IXANY);

    options.c_iflag &= ~(IGNPAR|ICRNL);

    /* output raw data */
    options.c_oflag &= ~OPOST;

    /* disablestd input */
    options.c_lflag &= ~(ICANON|ECHO|ECHOE|ISIG);

    /* clean the current setting */
    tcflush(serial_fd, TCIFLUSH);

    /* Enable the new setting right now */
    tcsetattr(serial_fd, TCSANOW, &options);
```

之後的部分，如讀寫則與一般網路程式設計大同小異，可利用 descriptor 進行讀寫，以及透過 select() 函式之類的方式檢查 serial port 是否有資料可讀寫。\
select() 的使用請參考 [Beej's Guide to Network Programming 正體中文版](https://web.archive.org/web/20200306075410/http://beej-zhtw.netdpi.net/)的第七章。

參考文獻

\[1] Michael Kerrisk, [The Linux Programming Interface](https://web.archive.org/web/20200306075410/http://www.books.com.tw/exep/assp.php/vip--af000078707/exep/prod/booksfile.php?item=F011849348), 2010.\
\[2] Michael R. Sweet, Serial Programming Guide for POSIX Operating Systems, 5th Edition.
