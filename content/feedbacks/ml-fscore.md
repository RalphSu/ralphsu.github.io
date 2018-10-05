
机器学习的常见指标：
两个最常见的衡量指标是“准确率（precision）”（你给出的结果有多少是正确的）和“召回率（recall）”（正确的结果有多少被你给出了）
这两个通常是此消彼长的（trade off），很难兼得。很多时候用参数来控制，通过修改参数则能得出一个准确率和召回率的曲线（ROC），这条曲线与x和y轴围成的面积就是AUC（ROC Area）。AUC可以综合衡量一个预测模型的好坏，这一个指标综合了precision和recall两个指标。
但AUC计算很麻烦，有人用简单的F-score来代替。F-score计算方法很简单：
F-score=(2*precision*recall)/(precision+recall)
即使不是算数平均，也不是几何平均。可以理解为几何平均的平方除以算术平均。

