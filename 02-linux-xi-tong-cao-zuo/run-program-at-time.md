# 自動執行程式

\[Method 1]

Enable the atd daemon

## /etc/init.d/atd start

Set the time $ at 8pm

Set command, Press CTRL+D $ at> Command

Sample format of time:

Run job at 6am on monday:

$ at 6am monday

Run job in 5 minutes time:

$ at now + 5 minutes

Run job at 4pm but 3 days later:

$ at 4pm + 3 days

Run job at 10am on 31st July:

$ at 10am Jul 31

\[Method 2]

Edit /etc/crontab, and set the time

In this example, the command is /sbin/reboot.

0 \* \* \* \* /sbin/reboot

Reference:

http://www.cyberciti.biz/tips/howto-shutdown-linux-box-automatically.html&#x20;
