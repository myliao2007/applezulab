# C struct 的使用



vsftpd-2.0.3

```c
333 static void
334 copy_string_settings(void)
335 {
338   const struct parseconf_str_setting* p_str_setting = parseconf_str_array;
339   while (p_str_setting->p_setting_name != 0)
340   {
341     if (*p_str_setting->p_variable != 0)
342     {
343       *p_str_setting->p_variable =
344       vsf_sysutil_strdup(*p_str_setting->p_variable);
345     }
346     p_str_setting++;
347    }
348 }
```

\
定義 struct 時也接著定義該 struct 的 variable

```c
99
100 static struct parseconf_uint_setting
101 {
102   const char* p_setting_name;
103   unsigned int* p_variable;
104 }
105 parseconf_uint_array[] =
106 { const char* p_setting_name, unsigned int* p_variable;
107 { "accept_timeout", &tunable_accept_timeout },
108 { "connect_timeout", &tunable_connect_timeout },
109 { "local_umask", &tunable_local_umask },
110 { "anon_umask", &tunable_anon_umask },
111 { "ftp_data_port", &tunable_ftp_data_port },
112 { "idle_session_timeout", &tunable_idle_session_timeout },
113 { "data_connection_timeout", &tunable_data_connection_timeout },
114 { "pasv_min_port", &tunable_pasv_min_port },
115 { "pasv_max_port", &tunable_pasv_max_port },
116 { "anon_max_rate", &tunable_anon_max_rate },
117 { "local_max_rate", &tunable_local_max_rate },
118 { "listen_port", &tunable_listen_port },
119 { "max_clients", &tunable_max_clients },
120 { "file_open_mode", &tunable_file_open_mode },
121 { "max_per_ip", &tunable_max_per_ip },
122 { "trans_chunk_size", &tunable_trans_chunk_size },
123 { 0, 0 }
124 };
125
```

\
下面這段是參考 C++ primer Plus, 4/E 寫的範例：\
\
可在宣告 struct 時，順道給定義兩個變數：John and Mary

```c
struct student
{
  char class;
  int num;
} John, Mary;
```

\
可以在宣告 struct 的時候，一起定義變數，且給定初值：

```c
struct student
{
  char class;
  int num;
} John =
{
  'A',     
  10
};
```

\
下列為宣告為陣列，並給定初值：

```c
struct student stu[2] =          
{
  {'A', 1},  
  {'B', 2} 
};
```
