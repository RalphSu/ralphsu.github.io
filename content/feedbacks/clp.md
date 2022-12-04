# clp日志压缩

我们在做监控日志平台时提出两个目标：高压缩率并且不放弃聚合查询的效率；为通用应用/文本日志找出错误模式以服务应用状态自动感知（发布和回滚）。

为了达到目的#1，我们放弃了elastic search，选择了clickhouse（enable zstd）或自己写hdfs（enable zstd）。而为了目的#2，有两种做法，一种是让用户交互式配置不同的日志的模式，在日志落盘前进行解析（靠近日志源的地方分布式处理，集中式处理很容易成为瓶颈难以维护和扩展），另一种想法是通过自动分析出重复的pattern，主要想通过论文中的一些日志解析模块。参考的论文有：
- logpai
- logMerge
- clp

最近clp的这篇帖子（来自uber）介绍了通过clp进行日志解析，并提高了存储效率的做法。本文就介绍clp的论文原理和主要做法。



## 应用
https://www.uber.com/en-FR/blog/reducing-logging-cost-by-two-orders-of-magnitude-using-clp/ 