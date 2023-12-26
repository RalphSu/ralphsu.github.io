
记录一下做resource insights的dshbaord时收集的材料

一开始去看用哪些metrics，想看kubestats和metric server里的指标差异，发现了komodor.com 和 kubecost还有kubefin。
komodor - 一个做kube运维和管理的商业方案，里面有关于优化的文档，有几个博客不错
https://komodor.com/learn/kubernetes-cost-optimization-cost-factors-challenges-solutions/
Tess如果来说要是做成开源产品，那就是这种东西，当然鉴于大部分人都上公有云，那么tess master的很多provision功能就可以去掉了。
https://www.opencost.io/ - 这个和Jesse Meng老师在腾讯云做的kubefin/crane就些类似了。这些cost monitoring还是得有一个价格模型。

我们的核心是看机器的利用率，那么顺便了解一下行业的机器资源利用率是多少，预期从一些正式一点的white paper，Gartner marketing research或者正式发表的industry track的论文中获取。
https://www.nrdc.org/sites/default/files/data-center-efficiency-assessment-IP.pdf
https://aws.amazon.com/blogs/aws/cloud-computing-server-utilization-the-environment/ : `The 2014 Data Center Efficiency Assessment from the NRDC has cloud server utilization at 65% and on-premises utilization running 12 to 18%, which is consistent with other estimates I’ve come across over the years. `

https://www.gartner.com/en/newsroom/press-releases/2022-12-06-how-to-make-the-data-center-eco-friendly : `To potentially reduce costs, I&O leaders should assess their server utilization. It is often less than 50%, if not as low as 20%; `

https://ieeexplore.ieee.org/document/6118751 : 
A Measurement Study of Server Utilization in Public Clouds 很对题，需要ieee账号才能下。这篇文章的citation也都是相关的，可以看做了做utilization研究的必引之作。2015-2017年间不少文章，其中谷歌这篇最离谱（https://dl.acm.org/doi/10.1145/2749469.2749475, 300多引用）：`Heracles using production latency-critical and batch workloads from Google and demonstrate average server utilizations of 90% without latency violations across all the load and colocation scenarios that we evaluated.`

了解下来，除了机器的，还有更总体的energy efficiceny，这个就有点多了，只留链接，不深入看。
https://www.sciencedirect.com/science/article/abs/pii/S0378778822004790 - A review of energy efficiency evaluation metrics for data centers
PUE的衡量概念 PUE (power usage effectiveness): A concept developed by the Green Grid and now widely considered the de facto industry standard for performance
measurement, power usage efficiency is a ratio of the amount of energy a data center consumes relative to the amount its
IT equipment uses. If no power losses were to occur and no additional power were required for cooling, the PUE would be
1.0. A PUE of 2.0 means that for every watt used by IT equipment, another watt is used by cooling, power distribution, and
lighting equipment.
还顺便看了篇 data center hardware tech refresh cycle的文章..
