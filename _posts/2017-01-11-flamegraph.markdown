

FlameGraph是图形化做perf tunning的工具。用于显示cpu/mem调用已经堆栈情况。主要资料来自http://www.brendangregg.com/flamegraphs.html。本文主要介绍怎么让系统调用和jvm的栈调用在同一个flame graph里显示。基本上等同于http://techblog.netflix.com/2015/07/java-in-flames.html的翻译。感谢netflix，感谢cctv。

系统级的perf工具有很多，比如linux的perf，systemtap，ktap； mac/bsd的dtrace；windows上的xperf.exe。
一般来说Java这种虚拟机的栈是没有办法和系统栈连起来的，主要原因
- JVM进行jit，符号表对系统级profiler不可见
- JVM把frame pointer寄存器也当做普通寄存器使用（也就是栈帧寄存器会被破坏，导致传统的栈回溯不能用）

这两个问题的解决办法是

- linux perf支持读取JIT 符号表，具体就是虚拟机生成/tmp/perf-PID.map文件，里面有符号表信息。perf命令可以来读取这里的信息做符号转换。Java通过perf-map-agent来生成符号表，可以在jvm起来时通过jvmTI使用，或者即使jvm开始跑了也可以dump出符号表信息来。这样jvm程序可以随时被Profile。 不过有时候符号表会随着运行时间变化，但是这个一般变化不大，所以问题不大。
- 简单来说，JVM支持一个多余的选项来禁用frame pointer寄存器的占用。伴有可能的0% - 3%的性能损失（具体例子要自己测试好）。JDK9 and JDK8 (JDK8 update 60 build 19) 后支持-XX:+PreserveFramePointer.

步骤(assume ubunut)：
1. sudo apt-get install perf (以及任何想要的kernel tools)
2. install JDK 1.8.0_60 b19以上
3. perf-map-agent

```
apt-get install cmake
export JAVA_HOME=/path-to-your-new-jdk8
git clone --depth=1 https://github.com/jrudolph/perf-map-agent
cd perf-map-agent
cmake .
make
```
This will generate jar and so in out/ folder.

4. FlameGraph
```
git clone --depth=1 https://github.com/brendangregg/FlameGraph

```

5. 启动Java程序
增加JVM选项-XX:+PreserveFramePointer，比如
```
java -XX:+PreserveFramePointer -jar ./target/dependency/alert-service-0.5.0-incubating-20161230.000413-247-shaded.jar server
```

6. 生成 Flame Graph
```
sudo perf record -F 99 -a -g -- sleep 30 

java -cp ~/repositories/perf-map-agent/out/attach-main.jar:$JAVA_HOME/lib/tools.jar net.virtualvoid.perf.AttachOnce 22585

sudo chown root /tmp/perf-*.map

sudo perf script | ./stackcollapse-perf.pl | ./flamegraph.pl --color=java --hash > flamegraph.svg
```
这里有几个注意事项，pl文件路径需要队。另外per-map-agent的.so需要能load到，上面的例子是在FlameGraph的代码目录里跑的，而且perf-map-agent的out/**.so 复制到了FlameGraph的代码目录下。

7. 用浏览器打开flamegraph.svg即可。





