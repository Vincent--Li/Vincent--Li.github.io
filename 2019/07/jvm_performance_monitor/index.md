# JVM性能监控工具


### 系统性能监控
- uptime
1. 系统时间
2. 运行时间
3. 连接数: 每个终端算一个连接数
4. 1/5/15分钟系统平均负载

- top
1. 第一行同uptime
2. cpu内存
3. 每个进程占CPU的情况

- vmstat
1. 可以统计系统的CPU, 内存, swap, io等情况
2. CPU占用率很高, 上下文切换频繁, 说明系统有线程正在频繁切换

- pidstat
1. 细致观察进程
2. 需要安装
3. 监控CPU
4. 监控IO
5. 监控内存

### Java自带工具
jdk安装目录下的tools目录下. 

#### jps
- 列出java进程, 类似ps命令
- -q指定jps只输出进程ID, 不输出类的短名称
- -m输出传递给Java进程(主函数)的参数
- -l可以用于输出主函数的完整路径
- -v可以显式传递给JVM的参数

#### jinfo

- 可以用来查看正在运行的Java应用程序的扩展参数, 甚至支持在运行时,修改部分参数
- -flag \<name\> : 打印指定的JVM的参数值
- -flag [+|-] \<name\>: 指定JVM参数的布尔值
- -flag \<name\>=\<value\>: 设置指定JVM参数的值

```shell
# 显示新生代对象晋升到老年代对象的最大年龄
jinfo -flag MaxTenuringThreshold 2972
-XX:MaxTenuringThreshold=15

# 显示是否打印GC详细信息
jinfo -flag PrintGCDetails 2972
-XX:-PrintGCDetails

# 运行时修改参数, 控制是否输出GC日志
jinof -flag PrintGCDetails 2972
-XX:-PrintGCDetails

jinfo -flag +PrintGCDetails 2972

jinfo -flag PrintGCDetails 2972
-XX:+PrintGCDetails
```

#### jmap

- 生成Java应用程序的堆快照和对象的统计信息
- jmap -histo 2972 >c:\s.txt
- dump堆出来
- jamp -dump:format=b,file=c:\heap.hprof 2972

#### jstack
- 打印线程dump
- -l打印锁信息
- -m打印java和native的帧信息
- -F强制dump,当jstack没有响应时使用

#### JConsole
- 图形化监控工具
- 可以查看Java应用程序的运行概况, 监控堆信息,永久区使用情况, 类加载情况等

#### Visual VM
- jvisualvm
- Visual VM: 是一种功能强大的多合一故障诊断和性能监控工具

### TODO
- TProfiler
