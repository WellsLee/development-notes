# 时钟
* CLOCK_MONOTONIC: 单调时间，实际上它指的是系统启动以后流逝的时间，这是由变量jiffies来记录的。系统每次启动时jiffies初始化为0，每来一个timer interrupt，jiffies加1，也就是说它代表系统启动后流逝的tick数。
* CLOCK_REALTIME: 挂钟时间，实际上就是指的是现实的时间，这是由变量xtime来记录的。系统每次启动时将CMOS上的RTC时间读入xtime，这个值是"自1970-01-01起经历的秒数、本秒中经历的纳秒数"，每来一个timer interrupt，也需要去更新xtime。
* wall time不一定是单调递增的。因为wall time是指现实中的实际时间，如果系统要与网络中某个节点时间同步、或者由系统管理员觉得这个wall time与现实时间不一致，有可能任意的改变这个wall time。最简单的例子是，我们用户可以去任意修改系统时间，这个被修改的时间应该就是wall time，即xtime，它甚至可以被写入RTC而永久保存。
