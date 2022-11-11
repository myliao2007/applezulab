# \[C] Linux 取得 HD 序號

```c
#include <stdio.h>
#include <linux/hdreg.h>
#include <fcntl.h>

int main(int argc,char **argv)
{
    int fd;
    struct hd_driveid id;
    fd = open (argv[1], O_RDONLYO_NONBLOCK);

    if ( ioctl(fd, HDIO_GET_IDENTITY, &id)) {

        printf("ERROR!!\n");
        exit(1);
    }

    printf("%s serial-> %s\n", argv[1], id.serial_no);

    close( fd );
}
```
