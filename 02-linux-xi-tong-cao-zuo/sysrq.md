# sysrq

Last update : 2010/11/16

Linux Kernel Documentation::sysrq.txt 1 Linux Magic System Request Key Hacks\
2 Documentation for sysrq.c\
3 \
4 \*  What is the magic SysRq key?\
5 \~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\
6 It is a 'magical' key combo you can hit which the kernel will respond to\
7 regardless of whatever else it is doing, unless it is completely locked up.\
8 \
9 \*  How do I enable the magic SysRq key?\
10 \~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\
11 You need to say "yes" to 'Magic SysRq key (CONFIG\_MAGIC\_SYSRQ)' when\
12 configuring the kernel. When running a kernel with SysRq compiled in,\
13 /proc/sys/kernel/sysrq controls the functions allowed to be invoked via\
14 the SysRq key. By default the file contains 1 which means that every\
15 possible SysRq request is allowed (in older versions SysRq was disabled\
16 by default, and you were required to specifically enable it at run-time\
17 but this is not the case any more). Here is the list of possible values\
18 in /proc/sys/kernel/sysrq:\
19    0 - disable sysrq completely\
20    1 - enable all functions of sysrq\
21   >1 - bitmask of allowed sysrq functions (see below for detailed function\
22        description):\
23           2 - enable control of console logging level\
24           4 - enable control of keyboard (SAK, unraw)\
25           8 - enable debugging dumps of processes etc.\
26          16 - enable sync command\
27          32 - enable remount read-only\
28          64 - enable signalling of processes (term, kill, oom-kill)\
29         128 - allow reboot/poweroff\
30         256 - allow nicing of all RT tasks\
31 \
32 You can set the value in the file by the following command:\
33     echo "number" >/proc/sys/kernel/sysrq\
34 \
35 Note that the value of /proc/sys/kernel/sysrq influences only the invocation\
36 via a keyboard. Invocation of any operation via /proc/sysrq-trigger is always\
37 allowed (by a user with admin privileges).\
38 \
39 \*  How do I use the magic SysRq key?\
40 \~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\
41 On x86   - You press the key combo 'ALT-SysRq-\<command key>'. Note - Some\
42            keyboards may not have a key labeled 'SysRq'. The 'SysRq' key is\
43            also known as the 'Print Screen' key. Also some keyboards cannot\
44     handle so many keys being pressed at the same time, so you might\
45     have better luck with "press Alt", "press SysRq", "release SysRq",\
46     "press \<command key>", release everything.\
47 \
48 On SPARC - You press 'ALT-STOP-\<command key>', I believe.\
49 \
50 On the serial console (PC style standard serial ports only) -\
51            You send a BREAK, then within 5 seconds a command key. Sending\
52            BREAK twice is interpreted as a normal BREAK.\
53 \
54 On PowerPC - Press 'ALT - Print Screen (or F13) - \<command key>,  \
55              Print Screen (or F13) - \<command key> may suffice.\
56 \
57 On other - If you know of the key combos for other architectures, please\
58            let me know so I can add them to this section.\
59 \
60 On all -  write a character to /proc/sysrq-trigger.  e.g.:\
61 \
62   echo t > /proc/sysrq-trigger\
63 \
64 \*  What are the 'command' keys?\
65 \~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\
66 'b'     - Will immediately reboot the system without syncing or unmounting\
67           your disks.\
68 \
69 'c' - Will perform a system crash by a NULL pointer dereference.\
70           A crashdump will be taken if configured.\
71 \
72 'd' - Shows all locks that are held.\
73 \
74 'e'     - Send a SIGTERM to all processes, except for init.\
75 \
76 'f' - Will call oom\_kill to kill a memory hog process.\
77 \
78 'g' - Used by kgdb on ppc and sh platforms.\
79 \
80 'h'     - Will display help (actually any other key than those listed\
81           here will display help. but 'h' is easy to remember :-)\
82 \
83 'i'     - Send a SIGKILL to all processes, except for init.\
84 \
85 'j'     - Forcibly "Just thaw it" - filesystems frozen by the FIFREEZE ioctl.\
86 \
87 'k'     - Secure Access Key (SAK) Kills all programs on the current virtual\
88           console. NOTE: See important comments below in SAK section.\
89 \
90 'l'     - Shows a stack backtrace for all active CPUs.\
91 \
92 'm'     - Will dump current memory info to your console.\
93 \
94 'n' - Used to make RT tasks nice-able\
95 \
96 'o'     - Will shut your system off (if configured and supported).\
97 \
98 'p'     - Will dump the current registers and flags to your console.\
99 \
100 'q'     - Will dump per CPU lists of all armed hrtimers (but NOT regular\
101           timer\_list timers) and detailed information about all\
102           clockevent devices.\
103 \
104 'r'     - Turns off keyboard raw mode and sets it to XLATE.\
105 \
106 's'     - Will attempt to sync all mounted filesystems.\
107 \
108 't'     - Will dump a list of current tasks and their information to your\
109           console.\
110 \
111 'u'     - Will attempt to remount all mounted filesystems read-only.\
112 \
113 'v' - Dumps Voyager SMP processor info to your console.\
114 \
115 'w' - Dumps tasks that are in uninterruptable (blocked) state.\
116 \
117 'x' - Used by xmon interface on ppc/powerpc platforms.\
118 \
119 'z' - Dump the ftrace buffer\
120 \
121 '0'-'9' - Sets the console log level, controlling which kernel messages\
122           will be printed to your console. ('0', for example would make\
123           it so that only emergency messages like PANICs or OOPSes would\
124           make it to your console.)\
125 \
126 \*  Okay, so what can I use them for?\
127 \~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\
128 Well, un'R'aw is very handy when your X server or a svgalib program crashes.\
129 \
130 sa'K' (Secure Access Key) is useful when you want to be sure there is no\
131 trojan program running at console which could grab your password\
132 when you would try to login. It will kill all programs on given console,\
133 thus letting you make sure that the login prompt you see is actually\
134 the one from init, not some trojan program.\
135 IMPORTANT: In its true form it is not a true SAK like the one in a :IMPORTANT\
136 IMPORTANT: c2 compliant system, and it should not be mistaken as   :IMPORTANT\
137 IMPORTANT: such.                                                   :IMPORTANT\
138        It seems others find it useful as (System Attention Key) which is\
139 useful when you want to exit a program that will not let you switch consoles.\
140 (For example, X or a svgalib program.)\
141 \
142 re'B'oot is good when you're unable to shut down. But you should also 'S'ync\
143 and 'U'mount first.\
144 \
145 'C'rash can be used to manually trigger a crashdump when the system is hung.\
146 Note that this just triggers a crash if there is no dump mechanism available.\
147 \
148 'S'ync is great when your system is locked up, it allows you to sync your\
149 disks and will certainly lessen the chance of data loss and fscking. Note\
150 that the sync hasn't taken place until you see the "OK" and "Done" appear\
151 on the screen. (If the kernel is really in strife, you may not ever get the\
152 OK or Done message...)\
153 \
154 'U'mount is basically useful in the same ways as 'S'ync. I generally 'S'ync,\
155 'U'mount, then re'B'oot when my system locks. It's saved me many a fsck.\
156 Again, the unmount (remount read-only) hasn't taken place until you see the\
157 "OK" and "Done" message appear on the screen.\
158 \
159 The loglevels '0'-'9' are useful when your console is being flooded with\
160 kernel messages you do not want to see. Selecting '0' will prevent all but\
161 the most urgent kernel messages from reaching your console. (They will\
162 still be logged if syslogd/klogd are alive, though.)\
163 \
164 t'E'rm and k'I'll are useful if you have some sort of runaway process you\
165 are unable to kill any other way, especially if it's spawning other\
166 processes.\
167 \
168 "'J'ust thaw it" is useful if your system becomes unresponsive due to a frozen\
169 (probably root) filesystem via the FIFREEZE ioctl.\
170 \
171 \*  Sometimes SysRq seems to get 'stuck' after using it, what can I do?\
172 \~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\
173 That happens to me, also. I've found that tapping shift, alt, and control\
174 on both sides of the keyboard, and hitting an invalid sysrq sequence again\
175 will fix the problem. (i.e., something like alt-sysrq-z). Switching to another\
176 virtual console (ALT+Fn) and then back again should also help.\
177 \
178 \*  I hit SysRq, but nothing seems to happen, what's wrong?\
179 \~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\
180 There are some keyboards that produce a different keycode for SysRq than the\
181 pre-defined value of 99 (see KEY\_SYSRQ in include/linux/input.h), or which\
182 don't have a SysRq key at all. In these cases, run 'showkey -s' to find an\
183 appropriate scancode sequence, and use 'setkeycodes \<sequence> 99' to map\
184 this sequence to the usual SysRq code (e.g., 'setkeycodes e05b 99'). It's\
185 probably best to put this command in a boot script. Oh, and by the way, you\
186 exit 'showkey' by not typing anything for ten seconds.\
187 \
188 \*  I want to add SysRQ key events to a module, how does it work?\
189 \~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\
190 In order to register a basic function with the table, you must first include\
191 the header 'include/linux/sysrq.h', this will define everything else you need.\
192 Next, you must create a sysrq\_key\_op struct, and populate it with A) the key\
193 handler function you will use, B) a help\_msg string, that will print when SysRQ\
194 prints help, and C) an action\_msg string, that will print right before your\
195 handler is called. Your handler must conform to the prototype in 'sysrq.h'.\
196 \
197 After the sysrq\_key\_op is created, you can call the kernel function\
198 register\_sysrq\_key(int key, struct sysrq\_key\_op \*op\_p); this will\
199 register the operation pointed to by 'op\_p' at table key 'key',\
200 if that slot in the table is blank. At module unload time, you must call\
201 the function unregister\_sysrq\_key(int key, struct sysrq\_key\_op \*op\_p), which\
202 will remove the key op pointed to by 'op\_p' from the key 'key', if and only if\
203 it is currently registered in that slot. This is in case the slot has been\
204 overwritten since you registered it.\
205 \
206 The Magic SysRQ system works by registering key operations against a key op\
207 lookup table, which is defined in 'drivers/char/sysrq.c'. This key table has\
208 a number of operations registered into it at compile time, but is mutable,\
209 and 2 functions are exported for interface to it:\
210  register\_sysrq\_key and unregister\_sysrq\_key.\
211 Of course, never ever leave an invalid pointer in the table. I.e., when\
212 your module that called register\_sysrq\_key() exits, it must call\
213 unregister\_sysrq\_key() to clean up the sysrq key table entry that it used.\
214 Null pointers in the table are always safe. :)\
215 \
216 If for some reason you feel the need to call the handle\_sysrq function from\
217 within a function called by handle\_sysrq, you must be aware that you are in\
218 a lock (you are also in an interrupt handler, which means don't sleep!), so\
219 you must call \_\_handle\_sysrq\_nolock instead.\
220 \
221 \*  When I hit a SysRq key combination only the header appears on the console?\
222 \~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\
223 Sysrq output is subject to the same console loglevel control as all\
224 other console output.  This means that if the kernel was booted 'quiet'\
225 as is common on distro kernels the output may not appear on the actual\
226 console, even though it will appear in the dmesg buffer, and be accessible\
227 via the dmesg command and to the consumers of /proc/kmsg.  As a specific\
228 exception the header line from the sysrq command is passed to all console\
229 consumers as if the current loglevel was maximum.  If only the header\
230 is emitted it is almost certain that the kernel loglevel is too low.\
231 Should you require the output on the console channel then you will need\
232 to temporarily up the console loglevel using alt-sysrq-8 or:\
233 \
234     echo 8 > /proc/sysrq-trigger\
235 \
236 Remember to return the loglevel to normal after triggering the sysrq\
237 command you are interested in.\
238 \
239 \*  I have more questions, who can I ask?\
240 \~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\
241 And I'll answer any questions about the registration system you got, also\
242 responding as soon as possible.\
243  -Crutcher\
244 \
245 \*  Credits\
246 \~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\~\
247 Written by Mydraal <[vulpyne@vulpyne.net](https://web.archive.org/web/20200306075610/mailto:vulpyne@vulpyne.net)>\
248 Updated by Adam Sulmicki <[adam@cfar.umd.edu](https://web.archive.org/web/20200306075610/mailto:adam@cfar.umd.edu)>\
249 Updated by Jeremy M. Dolan <[jmd@turbogeek.org](https://web.archive.org/web/20200306075610/mailto:jmd@turbogeek.org)> 2001/01/28 10:15:59\
250 Added to by Crutcher Dunnavant <[crutcher+kernel@datastacks.com](https://web.archive.org/web/20200306075610/mailto:crutcher+kernel@datastacks.com)>
