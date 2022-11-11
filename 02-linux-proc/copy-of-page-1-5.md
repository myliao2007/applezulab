# 從應用層程式取得傳輸層 TCP 資訊

Aaron Liao，2014\
目的：\
\
當設計伺服器網路應用程式時，由於應用層本身並沒有網路底層的資訊，因此當有異常發生時，無法立即直接知道連線的資訊，通常需要藉由評估資料收送狀況、逾時（timeout）等方式間接得知。若伺服器應用程式可以取得得知目前 TCP 的狀態，就能知道連線是否還存在著，即使只是 TCP 連線狀態的改變，都能清楚知曉，而伺服器能提早對該連線進行處理。\
\
我們可以在 netinet/tcp.h 表頭檔案找到 TCP 資訊的定義。（在 Linux 系統的絕對路徑位於 /usr/include/netinet/tcp.h）。\
\
若你想要取得 TCP 連線的狀態，你可以引用這個表頭檔。\
\


```c
#include <netinet/tcp.h> 

50 #define TCP_INFO         11 /* Information about this connection. */   
```

\
這邊是 TCP 資訊的資料結構 \
\


```c
199 struct tcp_info 
200 { 
201   u_int8_t  tcpi_state; 
202   u_int8_t  tcpi_ca_state; 
203   u_int8_t  tcpi_retransmits; 
204   u_int8_t  tcpi_probes; 
205   u_int8_t  tcpi_backoff; 
206   u_int8_t  tcpi_options; 
207   u_int8_t  tcpi_snd_wscale : 4, tcpi_rcv_wscale : 4; 
208 
209   u_int32_t tcpi_rto; 
210   u_int32_t tcpi_ato; 
211   u_int32_t tcpi_snd_mss; 
212   u_int32_t tcpi_rcv_mss; 
213 
214   u_int32_t tcpi_unacked; 
215   u_int32_t tcpi_sacked; 
216   u_int32_t tcpi_lost; 
217   u_int32_t tcpi_retrans; 
218   u_int32_t tcpi_fackets; 
219 
220   /* Times. */ 
221   u_int32_t tcpi_last_data_sent;
222   u_int32_t tcpi_last_ack_sent; /* Not remembered, sorry.  */ 
223   u_int32_t tcpi_last_data_recv; 
224   u_int32_t tcpi_last_ack_recv; 
225 
226   /* Metrics. */ 
227   u_int32_t tcpi_pmtu; 
228   u_int32_t tcpi_rcv_ssthresh; 
229   u_int32_t tcpi_rtt; 
230   u_int32_t tcpi_rttvar; 
231   u_int32_t tcpi_snd_ssthresh; 
232   u_int32_t tcpi_snd_cwnd; 
233   u_int32_t tcpi_advmss; 
234   u_int32_t tcpi_reordering; 
235 
236   u_int32_t tcpi_rcv_rtt;
237   u_int32_t tcpi_rcv_space; 
238 
239   u_int32_t tcpi_total_retrans; 
240 }; 
```

\
// Enum of TCP status\
\


```c
136 enum
137 {
138   TCP_ESTABLISHED = 1,
139   TCP_SYN_SENT,
140   TCP_SYN_RECV,
141   TCP_FIN_WAIT1,
142   TCP_FIN_WAIT2,
143   TCP_TIME_WAIT,
144   TCP_CLOSE,
145   TCP_CLOSE_WAIT,
146   TCP_LAST_ACK,
147   TCP_LISTEN,
148   TCP_CLOSING   /* now a valid state */
149 };
```

\
這邊用一個簡單的函式範例來說明如何取得 TCP 資訊。如果你的程式中的 socket descriptor 名為 sd，那麼你就能用 getsockopt() 取得 TCP 的資訊。這邊我將 getsockopt() system call 包到 get\_tcp\_state() 函式，而 get\_tcp\_state() 函式就會將 sd socket descriptor 的 TCP 的狀態傳回。\
\


```c
int get_tcp_state(int sd)
{
    struct tcp_info t_info; /* the data structure of the TCP information */
    socklen_t t_info_len = sizeof(t_info); 

    if( getsockopt( sd, IPPROTO_TCP, TCP_INFO, &t_info, &t_info_len) != -1) {

        return t_info.tcpi_state; /* the state of the 'sd' TCP connection */
    }

    return -1;
}
```

\
