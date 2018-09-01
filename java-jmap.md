# jmap(Memory Map)
 
jmap 21952
打印进程的内存信息

jmap -heap 21952
打印进程的堆栈配置及当前堆栈使用情况概要

jmap histo 21952
查看堆内存中的对象数目、大小统计直方图

jmap histo:live 21952
查看堆内存中的对象数目、大小统计直方图，带上live则只统计活对象

jmap -permstat 21952
打印永久代堆栈信息统计

jmap -dump:live,format=b,file=e:\gs.hprof 21952
用hprof二进制格式dump进程的堆栈信息，
NOTE：在创建文件的过程中，Java程序会暂停，因此不要在系统繁忙时执行

这种用法用的最多，生成出的dump文件可以使用VisualVM,HPJmeter或IBM HeapAnalyzer打开分析

-J 传递参数给jmap启动的jvm，比如：
jmap -J-Xmx512m 21952

