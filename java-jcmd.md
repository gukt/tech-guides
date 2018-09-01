
jcmd - Sends diagnostic command requests to a running Java Virtual Machine

查看帮助
`C:\Users\Administrator>jcmd GameServer help`

```
21552:
The following commands are available:
JFR.stop
JFR.start
JFR.dump
JFR.check
VM.native_memory
VM.commercial_features
GC.rotate_log
ManagementAgent.stop
ManagementAgent.start_local
ManagementAgent.start
Thread.print
GC.class_histogram
GC.heap_dump
GC.run_finalization
GC.run
VM.uptime
VM.flags
VM.system_properties
VM.command_line
VM.version
help

For more information about a specific command use 'help <command>'.
```
 
`jcmd GameServer VM.flags`
打印VM flags

```
21552:
XX:+FlightRecorder -XX:InitialHeapSize=266340672 -XX:MaxHeapSize=4263510016 -XX:+UnlockCommercialFeatures -XX:+UseCompressedOops -XX:+UseFastUnorderedTimeStamps -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC
```

打印系统属性
`jcmd GameServer system_properties`

打印线程信息
`jcmd GameServer Thread.print > e:\threads.dump`

打印VM启动参数
`jcmd GameServer command_line`

```
32599:
VM Arguments:
jvm_args: -Dserver.id=61 -Djdbc.catalog=inuyasha-server-dev1 -Xms512m -Xmx512m -XX:MaxPermSize=256m -Dcom.sun.management.jmxremote.port=5656 -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false 
java_command: com.xy.inuyasha.servers.world.GameServer
Launcher Type: SUN_STANDARD
```

dump堆栈信息（二进制的）
jcmd GameServer GC.heap_dump e:\gs.jcmd_heap_dump.hprof