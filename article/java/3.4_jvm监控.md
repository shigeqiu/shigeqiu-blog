# jvm监控


<!-- TOC -->

- [jvm监控](#jvm监控)
    - [官方文档](#官方文档)
    - [jps](#jps)
    - [jmap和jhat](#jmap和jhat)
    - [jstat](#jstat)
        - [class](#class)
        - [compiler](#compiler)
        - [gc](#gc)
        - [gccapacity](#gccapacity)
        - [gcnew](#gcnew)
        - [gcnewcapacity](#gcnewcapacity)
        - [gcold](#gcold)
        - [gcoldcapacity](#gcoldcapacity)
        - [gcmetacapacity](#gcmetacapacity)
        - [gcutil](#gcutil)
        - [printcompilation](#printcompilation)
    - [jstack](#jstack)
    - [GUI监控工具](#gui监控工具)
        - [eclipse memory analyzer](#eclipse-memory-analyzer)
    - [GC](#gc)
    - [堆内存分配](#堆内存分配)
    - [非堆内存分配](#非堆内存分配)

<!-- /TOC -->

## 官方文档

- Java平台，标准版工具参考
- https://docs.oracle.com/javase/8/docs/technotes/tools/unix/toc.html
- jstat 
- https://docs.oracle.com/javase/8/docs/technotes/tools/unix/jstat.html

http://www.importnew.com/all-posts


- [x]  Jps
- [x] jmap和jhat
- [x] Jstat
- [ ] Jstack
- [ ] jinfo


## jps

jps是jdk提供的一个查看当前java进程的小工具

命令格式：`jps [options ] [ hostid ] `

- -q：只显示pid
- -m：输出main method的参数
- -l：输出完全的包名，应用主类名，jar的完全路径名
- -v：输出传递给JVM的参数
- -V：输出通过flag文件传递到JVM中的参数(.hotspotrc文件或-XX:Flags=所指定的文件
- -Joption：传递参数到vm,例如:-J-Xms512m

> 列出远程服务器192.168.0.77机器所有的jvm实例，采用rmi协议，默认连接端口为1099  
> (前提是远程服务器提供jstatd服务）

## jmap和jhat

jmap是JDK自带的工具软件，主要用于打印指定Java进程(或核心文件、远程调试服务器)的共享对象内存映射或堆内存细节。可以使用jmap生成Heap Dump。



```
jmap -histo[:live] <pid>
```
打印堆的柱状图。其中包括每个Java类、对象数量、内存大小(单位：字节)、完全限定的类名。打印的虚拟机内部的类名称将会带有一个’\*’前缀。如果指定了live子选项，则只计算活动的对象。

```
jmap -dump:format=b,file=heapDump.hprof <pid>

jhat heapDump
```

将某个进程的堆dump文件导出到heapDump文件中。使用jhat命令，就启动了一个http服务，端口是7000，然后在访问`http://localhost:7000/`

**jhat**(Java Heap Analysis Tool),是一个用来分析java的堆情况的命令。之前的文章讲到过，使用jmap可以生成Java堆的Dump文件。生成dump文件之后就可以用jhat命令，将dump文件转成html的形式，然后通过http访问可以查看堆情况。

> 在此要注意，一般不会直接在服务器上进行分析，因为jhat是一个耗时并且耗费硬件资源的过程，一般把服务器生成的dump文件复制到本地或其他机器上进行分析。



## jstat

用于监控虚拟机各种运行状态信息的命令行工具。他可以显示本地或远程虚拟机进程中的类装载、内存、垃圾收集、JIT编译等运行数据，在没有GUI图形的服务器上，它是运行期定位虚拟机性能问题的首选工具。

一个极强的监视VM内存工具。可以用来监视VM内存内的各种堆和非堆的大小及其内存使用量。

``` sh
[www@idc02-waibao-huidian ]$ jstat -help
Usage: jstat -help|-options
       jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]

Definitions:
  <option>      An option reported by the -options option
  <vmid>        Virtual Machine Identifier. A vmid takes the following form:
                     <lvmid>[@<hostname>[:<port>]]
                Where <lvmid> is the local vm identifier for the target
                Java virtual machine, typically a process id; <hostname> is
                the name of the host running the target Java virtual machine;
                and <port> is the port number for the rmiregistry on the
                target host. See the jvmstat documentation for a more complete
                description of the Virtual Machine Identifier.
  <lines>       Number of samples between header lines.
  <interval>    Sampling interval. The following forms are allowed:
                    <n>["ms"|"s"]
                Where <n> is an integer and the suffix specifies the units as 
                milliseconds("ms") or seconds("s"). The default units are "ms".
  <count>       Number of samples to take before terminating.
  -J<flag>      Pass <flag> directly to the runtime system.
```

### class

`jstat -class pid`:显示加载class的数量，及所占空间等信息。

| 列名  | 说明  |
|---|---|
Loaded | 加载class的数量
Bytes | 所占用空间大小
Unloaded | 未加载数量
Bytes | 未加载占用空间
Time | 时间

### compiler

`jstat -compiler pid` :显示VM实时编译的数量等信息。

| 列名  | 说明  |
|---|---|
Compiled | 编译数量。
Failed | 失败数量
Invalid | 不可用数量
Time | 时间
FailedType  | 失败类型
FailedMethod | 失败的方法


### gc

`jstat -gc pid` :可以显示gc的信息，查看gc的次数，及时间。

| 列名  | 说明  |
|---|---|
S0C | 第一个幸存区的大小
S1C | 第二个幸存区的大小
S0U | 第一个幸存区的使用大小
S1U | 第二个幸存区的使用大小
EC | 伊甸园区的大小
EU | 伊甸园区的使用大小
OC | 老年代大小
OU | 老年代使用大小
MC | 方法区大小
MU | 方法区使用大小
CCSC | 压缩类空间大小
CCSU | 压缩类空间使用大小
YGC | 年轻代垃圾回收次数
YGCT | 年轻代垃圾回收消耗时间
FGC | 老年代垃圾回收次数
FGCT | 老年代垃圾回收消耗时间
GCT | 垃圾回收消耗总时间

### gccapacity

`jstat -gccapacity pid` 堆内存统计

| 列名  | 说明  |
|---|---|
NGCMN | 新生代最小容量
NGCMX | 新生代最大容量
NGC | 当前新生代容量
S0C | 第一个幸存区大小
S1C | 第二个幸存区的大小
EC | 伊甸园区的大小
OGCMN | 老年代最小容量
OGCMX | 老年代最大容量
OGC | 当前老年代大小
OC | 当前老年代大小
MCMN | 最小元数据容量
MCMX | 最大元数据容量
MC | 当前元数据空间大小
CCSMN | 最小压缩类空间大小
CCSMX | 最大压缩类空间大小
CCSC | 当前压缩类空间大小
YGC | 年轻代gc次数
FGC | 老年代GC次数


### gcnew

`jstat -gcnew pid`  新生代垃圾回收统计

| 列名  | 说明  |
|---|---|
S0C | 第一个幸存区大小
S1C | 第二个幸存区的大小
S0U | 第一个幸存区的使用大小
S1U | 第二个幸存区的使用大小
TT | 对象在新生代存活的次数
MTT | 对象在新生代存活的最大次数
DSS | 期望的幸存区大小
EC | 伊甸园区的大小
EU | 伊甸园区的使用大小
YGC | 年轻代垃圾回收次数
YGCT | 年轻代垃圾回收消耗时间

### gcnewcapacity

`jstat -gcnewcapacity pid `  new对象的信息及其占用量。新生代内存统计

| 列名  | 说明  |
|---|---|
NGCMN | 新生代最小容量
NGCMX | 新生代最大容量
NGC | 当前新生代容量
S0CMX | 最大幸存1区大小
S0C | 当前幸存1区大小
S1CMX | 最大幸存2区大小
S1C | 当前幸存2区大小
ECMX | 最大伊甸园区大小
EC | 当前伊甸园区大小
YGC | 年轻代垃圾回收次数
FGC | 老年代回收次数

### gcold

`jstat -gcold pid` : old对象的信息。 老年代垃圾回收统计

| 列名  | 说明  |
|---|---|
MC | 方法区大小
MU | 方法区使用大小
CCSC | 压缩类空间大小
CCSU | 压缩类空间使用大小
OC | 老年代大小
OU | 老年代使用大小
YGC | 年轻代垃圾回收次数
FGC | 老年代垃圾回收次数
FGCT | 老年代垃圾回收消耗时间
GCT | 垃圾回收消耗总时间

### gcoldcapacity

`jstat -gcoldcapacity pid` old对象的信息及其占用量。

| 列名  | 说明  |
|---|---|
OGCMN | 老年代最小容量
OGCMX | 老年代最大容量
OGC | 当前老年代大小
OC | 老年代大小
YGC | 年轻代垃圾回收次数
FGC | 老年代垃圾回收次数
FGCT | 老年代垃圾回收消耗时间
GCT | 垃圾回收消耗总时间

### gcmetacapacity

`jstat -gcmetacapacity pid`: 元数据空间统计

| 列名  | 说明  |
|---|---|
MCMN | 最小元数据容量
MCMX | 最大元数据容量
MC | 当前元数据空间大小
CCSMN | 最小压缩类空间大小
CCSMX | 最大压缩类空间大小
CCSC | 当前压缩类空间大小
YGC | 年轻代垃圾回收次数
FGC | 老年代垃圾回收次数
FGCT | 老年代垃圾回收消耗时间
GCT | 垃圾回收消耗总时间


### gcutil

`jstat -gcutil pid 1000 10 `: 1000ms统计一次gc情况统计10次；

| 列名  | 说明  |
|---|---|
S0 | 幸存1区当前使用比例
S1 | 幸存2区当前使用比例
E | 伊甸园区使用比例
O | 老年代使用比例
M | 元数据区使用比例
CCS | 压缩使用比例
YGC | 年轻代垃圾回收次数
FGC | 老年代垃圾回收次数
FGCT | 老年代垃圾回收消耗时间
GCT | 垃圾回收消耗总时间

### printcompilation

`jstat -printcompilation pid` :当前VM执行的信息。

| 列名  | 说明  |
|---|---|
Compiled | 最近编译方法的数量
Size | 最近编译方法的字节码数量
Type | 最近编译方法的编译类型。
Method | 方法名标识。

## jstack

**jstack** 是java虚拟机自带的一种堆栈跟踪工具。

- http://www.importnew.com/23761.html

jstack用于生成java虚拟机当前时刻的线程快照。线程快照是当前java虚拟机内每一条线程正在执行的方法堆栈的集合，生成线程快照的主要目的是定位线程出现长时间停顿的原因，如线程间死锁、死循环、请求外部资源导致的长时间等待等。 线程出现停顿的时候通过jstack来查看各个线程的调用堆栈，就可以知道没有响应的线程到底在后台做什么事情，或者等待什么资源。 如果java程序崩溃生成core文件，jstack工具可以用来获得core文件的java stack和native stack的信息，从而可以轻松地知道java程序是如何崩溃和在程序何处发生问题。另外，jstack工具还可以附属到正在运行的java程序中，看到当时运行的java程序的java stack和native stack的信息, 如果现在运行的java程序呈现hung的状态，jstack是非常有用的。

So , jstack 命令主要用来查看Java线程的调用堆栈的，可以用来分析线程问题（如死锁）。

## GUI监控工具

- (Java自带监控工具)JConsole
- (Java自带监控工具)Java VisualVM
- jvmstat
- YourKit Java Profiler
- Perfino
- Jprofiler
- eclipse memory analyzer


### eclipse memory analyzer

三个选项

- leak suspects report 泄漏疑似报告
- component report  组件报告
- re-open previously run reports  重新打开以前运行的报表

## GC

GC日志输出到控制台

| 启动参数  | 说明  |
|---|---|
-XX:+PrintGC | GC的简要信息
-XX:+PrintGCDetails | GC的详细信息
-XX:+PrintGCTimeStamps | GC的时间信息
-XX:+PrintGCApplicationStoppedTime | GC造成的应用暂停的时间

GC信息输出到指定文件

| 启动参数  | 说明  |
|---|---|
-Xloggc: gc.log | 将gc的信息输出到gc.log中


## 堆内存分配

- JVM初始分配的堆内存由-Xms指定，默认是物理内存的1/64；
- JVM最大分配的堆内存由-Xmx指定，默认是物理内存的1/4。
- 默认空余堆内存小于40%时，JVM就会增大堆直到-Xmx的最大限制；
- 空余堆内存大于70%时，JVM会减少堆直到-Xms的最小限制。
- 因此服务器一般设置-Xms、-Xmx 相等以避免在每次GC 后调整堆的大小。
- 说明：如果-Xmx 不指定或者指定偏小，应用可能会导致java.lang.OutOfMemory错误，此错误来自JVM，不是Throwable的，无法用try…catch捕捉。

## 非堆内存分配

- JVM使用`-XX:PermSize`设置非堆内存初始值，默认是物理内存的1/64；
- 由`XX:MaxPermSize`设置最大非堆内存的大小，默认是物理内存的1/4。

`XX:MaxPermSize`设置过小会导致`java.lang.OutOfMemoryError: PermGen space `就是内存益出。
为什么会内存益出：
1. 这一部分内存用于存放Class和Meta的信息，Class在被 Load的时候被放入PermGen space区域，它和存放Instance的Heap区域不同。
1. GC(Garbage Collection)不会在主程序运行期对PermGen space进行清理，所以如果你的APP会LOAD很多CLASS 的话,就很可能出现PermGen space错误。
1. 这种错误常见在web服务器对JSP进行pre compile的时候。