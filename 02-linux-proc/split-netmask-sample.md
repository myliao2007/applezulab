# \[C] 程式範例-切割 subnet



為了學校作業寫的程式，用途是可將一大 IP 網段切割為數個小 subnet 輸出\
\
問題敘述：試設計一程式，Input 為 IP與subnet的大小，Output為所有的 subnets 與 netmask。\
\
編譯環境：\
\
於UNIX, Linux, BSD, OS 上編譯：\
\


> gcc cidr.c -o cidr -Wall -lm

\>> 執行格式說明：\
\


> $ ./cidr \[IP address] \[number of hosts per subnet]\
> \
> \[ IP address ] 為IPv4位址\
> \
> \[ number of hosts per subnet ] 為每個子網域中的主機數目\
> \
> 範例：./cidr 192.168.0.0 8

\
流程說明\
\>> 取得INPUT資料：\
\
1\. 取得IP與每個subnet的host數目。\
\
2\. 檢查IP的格式是否正確。\
\
3\. 將十進位IP轉為二進位格式。\
\
4\. 檢查該IP屬於哪一種 class (A/B/C)。\
\
5\. 根據該IP的class取得預設之netmask。\
\
6\. 根據使用者輸入的 host 數目來求切割後subnet的netmask。\
\
7\. 計算該網段可切割為幾個subnet。\
\
8\. 輸出所有的subnet與netmask。\
\
\>> 輸出的資料儲存於”output.txt”。\
\
執行結果：\
\
說明：\


> \
> 1.$ ./cidr 192.168.1.0 8\
> \>> 代表192.168.1.0此網段要切成每個subnet有8個hosts。\
> \
> 2.IP: 192.168.0.0\
> \
> 3.IP (bin): 11000000 10101000 00000000 00000000 >> 轉成二進位的IP。\
> \
> 4.Class: C >> 代表該IP屬於Class C。\
> \
> 5.Default netmaek: 255.255.255.0 >> class C預設 netmask 為 255.255.255.0。\
> \
> 6.Default netmask (bin): 11111111 11111111 11111111 00000000 >> 轉成二進位。\
> \
> 7.\[Input] Host number/subnet: 5: >> 輸入每個subnet的hosts數目。\
> \
> 8.\[FixTo] Host number/subnet: 8 >> 將 5 修正為最接近的hosts合法數目 8。\
> \
> 9.Host id: 3 >> host id 的 bit 數。\
> \
> 10.Subnet netmaek: 255.255.255.248 >> 計算出的每個subnet netmask。\
> \
> 11.Subnet netmask (bin):\
> 11111111 11111111 11111111 11111000 >> 轉成二進\
> \
> 12.Subnet number: 32 >> 總共可以切割成 32 個 subnet。

\


```c
/* Date: May, 2006
*
* Input:
* IP and the size of subnet
* Output:
* All subnets and the netmask
*
* Ref.
* [1] TCP/IP Protocol suite 2nd, chap 4 and chap5
* [2] http://www.study-area.org/network/network_ip_addr.htm
*
* Aaron Liao
*/

#include <stdio.h>
#include <math.h>
#include <string.h>
#include <stdlib.h>


#define DEBUG 1
#define IP_ADDR_CLEN 16
#define IP_ADDR_BLEN 32

enum 
{ TRUE = 0, FALSE };

/* class A 0
* class B 10
* class C 110
* Class D 1110
* Class E 1111
*/

enum ip_type
{ classA = 0, classB, classC, classD, classE };

int chk_class (char *bip);
int out_class (unsigned char class);
int get_orig_mask (unsigned int class, char *mask, char *bmask);
int get_new_mask (unsigned int host_bit, char *mask, char *bmask);
int no_buf (void);
int chk_ip (char *ip);
int get_host_num (unsigned long long *host, char *num,
    unsigned int class, unsigned int *host_num);
int get_subnet_num (unsigned int class, unsigned int host_id,
      unsigned int *sub);
int get_IP (char *ip, char *arg, char *bip);
int dec2bin (unsigned char *ip, unsigned char *bip);
int bin2dec (unsigned char *bip, unsigned char *ip);
int out_bin (char *str);
int out_subnet (char *bip, char *mask, unsigned int subnet_num,
  unsigned int host_num, unsigned int host_id);
int usage (void);

int
chk_class (char *bip)
{
  if (!bip[0])
    return classA;

  if (!bip[1])
    return classB;

  if (!bip[2])
    return classC;

  if (!bip[3])
    return classD;

  return classE;
}

int
out_class (unsigned char class)
{
  printf ("\tClass: ");

  switch (class)
  {
    case classA:
      printf ("A\r\n");
      break;
    case classB:
      printf ("B\r\n");
      break;
    case classC:
      printf ("C\r\n");
      break;
    case classD:
      printf ("D\r\n");
      break;
    case classE:
      printf ("E\r\n");
      break;
    default:
/* should never */
      printf ("?\r\n");
      break;
    }

  return 0;
}

int
get_orig_mask (unsigned int class, char *mask, char *bmask)
{
  switch (class)
  {
    case classA:
      strcpy (mask, "255.0.0.0");
      break;
    case classB:
      strcpy (mask, "255.255.0.0");
      break;
    case classC:
      strcpy (mask, "255.255.255.0");
      break;
    default:
      break;
    }

  printf ("\tDefault netmaek: %s\r\n", mask);

  dec2bin (mask, bmask);
  printf ("\tDefault netmask (bin): ");
  out_bin (bmask);

  return 0;
}

int
get_new_mask (unsigned int host_bit, char *mask, char *bmask)
{
  unsigned int count, i;

  for (count = 0; count < IP_ADDR_BLEN; count++)
       bmask[count] = 1;

  for (count = (IP_ADDR_BLEN - 1), i = host_bit; i > 0; i--, count--)
      bmask[count] = 0;

  bin2dec (bmask, mask);

  printf ("\tSubnet netmaek: %s\r\n", mask);

  printf ("\tSubnet netmask (bin): ");
  out_bin (bmask);

  return 0;
}

int
no_buf (void)
{
  setbuf (stdout, 0);
  return 0;
}


int
chk_ip (char *ip)
{
  unsigned int count, i, state, error, count_num;
  char *ptr = NULL, tmp[IP_ADDR_CLEN];


/* check there are three '.' */
  for (i = 0, count = 0; i < IP_ADDR_CLEN; i++)
    {
      if (ip[i] == '.')
          count++;
    }

  if (count != 3)
    {
      printf ("\t--> Error: [IP][.][%d] Incorrect IP format.\n\n", count);
      return FALSE;
    }

/* Check [0-9] and '.' */
  for (i = 0; i < IP_ADDR_CLEN; i++)
    {

      if (ip[i] == '.' || (ip[i] >= '0' && ip[i] <= '9'))
           continue;

      if (ip[i] == '\0') /* End of string */
           break;

      printf ("\t--> Error: [IP] only [0-9] and '.'\r\n");
      return FALSE;
    }


  strcpy (tmp, ip);
  ptr = strtok (tmp, ".");

  while (ptr != NULL)
  {
      if (atoi (ptr) > 255)
      {
         printf ("\t--> Error: [IP][%d] Incorrect IP format.\n\n",
         atoi (ptr));
         return FALSE;
      }

      ptr = strtok (NULL, ".");

    }

/* check the order of number and '.'
* Format: number.number.number.number
*/
  for (i = 0, error = 0, state = 0, count = 0, count_num = 0; i <
       strlen (ip); i++)
  {


      switch (state)
      {

           case 0:  /* '.' at the head */
             if (ip[i] == '.')
               error = 5;
             else
               {
                 state = 1;
                 count_num++;
               }

             break;

           case 1:  /* mid-number */

             if (ip[i] == '.')
             {
                 state = 2;
                 count++;
                 count_num = 0;
             }
             else
             {
                 count_num++;
             }

     /* number is limited to three digits */
            if (count_num > 3)
               error = 1;

             break;

           case 2:  /* . */
          
             if (ip[i] == '.' || count > 3 || count_num > 3)
                  error = 2;

             state = 1;
             count_num++;
             break;

           default:
             error = 4;
             break;
      }

      if (error)
      {
        printf ("\t--> Error: [IP][Regular][%d] ", error);
        printf ("There are some errors.\r\n");
        return FALSE;
      }

    }

  if (ip[i - 1] == '.')
    {
      printf ("\t--> Error: [IP][.] Can't end with '.'\r\n");
      return FALSE;
    }

  return TRUE;
}

/*
* ip[]: "210.125.0.0"
* bip[]: 11010010011111010000000000000000
*/
int
dec2bin (unsigned char *ip, unsigned char *bip)
{
  char *ptr = NULL;
  unsigned char dec_ip[4] = { 0, 0, 0, 0 };
  int count, j;

  ptr = strtok (ip, ".");
  for (count = 0; count < 4; count++)
  {

      if (ptr != NULL)
      {

        dec_ip[count] = atoi (ptr);
        ptr = strtok (NULL, ".");

      }
      else
      {
          /* Error */
          return FALSE;
      }
   }

/* dec-to-binary */
  for (j = 3; j > (-1); j--)
  {
      for (count = 7; count > (-1); count--)
      {
        bip[8 * (j + 1) - count - 1] = ((dec_ip[j] & (1 <<(count))) > 0 ? 1 : 0);
      }
  }

       return TRUE;
  }

int
bin2dec (unsigned char *bip, unsigned char *ip)
{
  int i, j;
  unsigned int dec_ip[4] = { 0, 0, 0, 0 };

/* Support unsigned only */
  for (i = 0, j = 0; i < IP_ADDR_BLEN; i++)
  {
      unsigned char _offset = i % 8;

      if (_offset == 0 && i)
      {
        j++;
      }

      dec_ip[j] += (bip[i] <<(7 - _offset));
  }

  sprintf (ip, "%d.%d.%d.%d", dec_ip[0], dec_ip[1], dec_ip[2], dec_ip[3]);

  return 0;
}


/* Input: the number of hosts per subnet
* The number of host(s) must be the power of 2.
* 2^0, 2^1, ... , 2^32
*/
int
chk_host_num (unsigned long long num)
{
  unsigned int error, count;

/* Must be even */
  if (num % 2)
    return FALSE;

/* check if the power of 2 */
  for (count = 0, error = 1; count < 32; count++)
  {
      if (num == (1 << count))
           error = 0;
  }

  if (error)
    return FALSE;

  return 0;
}

int
get_host_num (unsigned long long *host, char *num,
       unsigned int class, unsigned int *host_id)
{
  unsigned int count;
  const unsigned int max_len = 32;
  unsigned long long max_hosts;

  switch (class)
  {
    case classA:
      max_hosts = pow (2, 24); /* powl */
      break;
    case classB:
      max_hosts = pow (2, 16);
      break;
    case classC:
      max_hosts = pow (2, 8);
      break;
    default:
      return FALSE;
      break;
  }

  for (count = 0; count < max_len && num[count] != '\0'; count++)
  {
      if (num[count] < '0' || num[count] > '9')
      {
         printf ("\t--> Error: [Host_num] limited to be [0-9]\r\n");
         return FALSE;
      }
  }

  (*host) = atoll (num);

  if ((*host) < 2)
  {
      printf ("\t--> Error: [Host_num][%llu] is too small.\r\n", (*host));
      return FALSE;
  }

  if ((*host) > pow (2, 32))
  {
      printf ("\t--> Error: [Host_num][%llu] is too large.\r\n", (*host));
      return FALSE;
  }

  if ((*host) > max_hosts)
  {
      printf ("\t--> Error: [Host_num][Max: %llu] %llu is too large.\r\n",max_hosts, (*host));
      return FALSE;
  }

  printf ("\t[Input] Host number/subnet: %llu\r\n", (*host));

  for (count = 0; count < 32; count++)
  {

      if ((*host) <= pow (2, count))
      {
        (*host) = pow (2, count);
        (*host_id) = count;
        break;
      }
  }

  printf ("\t[FixTo] Host number/subnet: %llu\r\n", (*host));
  printf ("\tHost id: %d\r\n", (*host_id));

  return 0;
}

int
get_IP (char *ip, char *arg, char *bip)
{

  if (strlen (arg) > IP_ADDR_CLEN)
  {
      printf ("\t--> Error: [IP] %s incorrect format.\r\n", arg);
      return FALSE;
  }


  if (strlen (arg) > IP_ADDR_CLEN)
       strncpy (ip, arg, IP_ADDR_CLEN);
  else
       strncpy (ip, arg, strlen (arg));

  if (chk_ip (ip))
       return FALSE;

  printf ("\tIP: %s\r\n", ip);

  dec2bin (ip, bip);
  printf ("\tIP (bin): ");
  out_bin (bip);

  return 0;
}

int
get_subnet_num (unsigned int class, unsigned int host_id, unsigned int *sub)
{
  unsigned int tmp;

  switch (class)
  {

    case classA:
      tmp = (24 - host_id);
      break;
    case classB:
      tmp = (16 - host_id);
      break;
    case classC:
      tmp = (8 - host_id);
      break;
    default:
      return FALSE;
      break;
  }

  (*sub) = pow (2, tmp);
  printf ("\tSubnet number: %d\r\n", (*sub));

  return 0;
}

int
out_bin (char *str)
{
  unsigned int count;

  for (count = 0; count < IP_ADDR_BLEN; count++)
  {

      printf ("%d", str[count]);

      if (count > 0 && !((count + 1) % 8))
           printf (" ");
  }

  printf ("\r\n");
  return 0;
}

int
out_subnet (char *bip, char *mask, unsigned int subnet_num,
     unsigned int host_num, unsigned int host_id)
{
  unsigned char ip[IP_ADDR_CLEN];
  unsigned int i, j, k;
  FILE *fp = NULL;

  if ((fp = fopen ("output.txt", "w")) == NULL)
    printf ("Can't open output.txt\r\n");

  if (fp)
  {
      fprintf (fp, "Output: \r\n");
      fprintf (fp, "netmask: [ %s ]\r\n", mask);
  }

  printf ("Output: \r\n");
  printf ("netmask: [ %s ]\r\n", mask);

  for (i = 0; i < subnet_num; i++)
  {

      bin2dec (bip, ip);

      if (fp)
          fprintf (fp, "Subnet %d: [ %s - ", i + 1, ip);

      printf ("Subnet %d: [ %s - ", i + 1, ip);

      for (j = 0; j < host_num - 1; j++)
      {

          /* binary increment */
          for (k = (IP_ADDR_BLEN - 1); k > 0; k--)
          {

             bip[k] = (!bip[k]);

             if (bip[k] == 1)
             break;
          }
      }

      bin2dec (bip, ip);

      if (fp)
          fprintf (fp, "%s ]\r\n", ip);

      printf ("%s ]\r\n", ip);

/* add 1 */
      for (k = (IP_ADDR_BLEN - 1); k > 0; k--)
      {

        bip[k] = (!bip[k]);

        if (bip[k] == 1)
          break;
      }

    }

  if (fp)
    fclose (fp);

  return 0;
}

int
usage (void)
{
  printf ("Usage: cidr [IP address] [number of hosts per subnet]\r\n");
  printf ("\t ex. cidr 192.168.0.0 8\r\n");
  return 0;
}

int
main (int argc, char *argv[])
{

  unsigned char ip[IP_ADDR_CLEN], bip[IP_ADDR_BLEN], mask[IP_ADDR_CLEN], bmask[IP_ADDR_BLEN];

/* The number of hosts should be even, and the power of two,
* But I allow the user to use any number of hosts between
* 2^0 and 2^32. Then I calculate the approach value. ex.
* They want 5 hosts per subnet, and I find 8 hosts per subnet.
*/
  unsigned long long host_num;
  unsigned int host_bit, ip_class, subnet_num;

/* clean the output.txt */
  fclose (fopen ("output.txt", "w"));

  memset (ip, '\0', IP_ADDR_CLEN);
  no_buf ();   /* Don't buffer the standard output */


  if (argc < 3 || argv[1] == NULL || argv[2] == NULL)
  {
      usage ();
      return FALSE;
  }


  if (get_IP (ip, argv[1], bip))
     return FALSE;

/* Get class of IP and normal netmask */
  ip_class = chk_class (bip);
  out_class (ip_class);

  if (ip_class == classD || ip_class == classE)
    return FALSE;

  get_orig_mask (ip_class, mask, bmask);

/* Get the number of hosts */
  if (get_host_num (&host_num, argv[2], ip_class, &host_bit))
    return FALSE;

/* calculate the new netmask of subnet */
  get_new_mask (host_bit, mask, bmask);

  get_subnet_num (ip_class, host_bit, &subnet_num);

  out_subnet (bip, mask, subnet_num, host_num, host_bit);

  return 0;
}
```

\
\


> ```
> 執行結果範例：
> ```

> ./cidr 192.168.0.0 87\
> \
> IP: 192.168.0.0\
> IP (bin): 11000000 10101000 00000000 00000000 \
> Class: C\
> Default netmaek: 255.255.255.0\
> Default netmask (bin): 11111111 11111111 11111111 00000000 \
> \[Input] Host number/subnet: 87\
> \[FixTo] Host number/subnet: 128\
> Host id: 7\
> Subnet netmaek: 255.255.255.128\
> Subnet netmask (bin): 11111111 11111111 11111111 10000000 \
> Subnet number: 2\
> Output: \
> netmask: \[ 255.255.255.128 ]\
> Subnet 1: \[ 192.168.0.0 - 192.168.0.127 ]\
> Subnet 2: \[ 192.168.0.128 - 192.168.0.255 ]
