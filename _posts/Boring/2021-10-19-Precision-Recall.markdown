---
layout: post
title:  准确率-召回率 - Precision-Recall
date:   2021-10-19 21:10:16 +0800
categories: 更无聊的人生
author: 崔秉龙
location: Guangzhou, China
---

# Precision-Recall

## 准确率-召回率

> 本文翻译自 [Precision-Recall](https://scikit-learn.org/stable/auto_examples/model_selection/plot_precision_recall.html#sphx-glr-auto-examples-model-selection-plot-precision-recall-py)
> 更多翻译可以看 [Scikit-learn 某部分的中文翻译](https://blog.csdn.net/BerryBC/article/details/120625800)



用于评估分类器输出质量的 `准确率-召回率` 指标示例。

当类别非常不平衡时， `准确率-召回率` 是衡量预测成功与否的有用指标。在信息检索中，准确率是衡量结果相关性的指标，而召回率是衡量返回多少真正相关结果的指标。

 `准确率-召回率` 曲线显示了不同阈值下`准确率`和`召回率`之间的权衡。曲线下方的高面积代表高召回率和高精度，其中高精度与低误报率相关，高召回率与低误报率相关。两者的高分表明分类器正在返回准确的结果（高精度），以及返回大部分阳性结果（高召回率）。


**召回率高但精度低**的系统会返回很多结果，但与训练标签相比，其大部分预测标签是不正确的。**精度高但召回率低**的系统恰恰相反，返回的结果很少，但与训练标签相比，它的大部分预测标签都是正确的。具有**高精度和高召回率**的理想系统将返回许多结果，并且所有结果都正确标记。

准确率 ( P ) 定义为真阳性数 (T~p~) 加上真阳性数加上假阳性数 (F~p~)。

$$
P= {T_p \above{1px} T_p+T_f}
$$

召回率 ( R ) 定义为真阳性数 (T~p~) 加上真阳性数加上假阴性数 (F~n~)。

$$
P= {T_p \above{1px} T_p+F_n}
$$

这些数量也与 (F~1~) 分数有关，该分数被定义为准确率和召回率的调和平均值。

$$
P= 2×{P×R \above{1px} P+R}
$$

请注意，`准确率`可能不会随着`召回率`而降低。准确率的定义表明降低分类器的**阈值**可以通过增加返回结果的数量来增加分母。如果之前阈值设置得太高，新的结果可能都是真阳性，这会提高准确率。如果之前的阈值大约正确或太低，进一步降低阈值会引入误报，从而降低准确率。

召回率的坟墓 T~p~+F~n~ 不依赖于分类器阈值。这意味着降低分类器阈值可能会通过增加真阳性结果的数量来提高召回率。降低阈值也有可能使召回率保持不变，而准确率会波动。

在图的阶梯区域可以观察到`召回率`和`准确率`之间的关系——在这些步骤的边缘，阈值的微小变化会大大降低精度，而召回率只有很小的提高。

平均精度 (Average precision - AP) 总结了这样一个图，即在每个**阈值**处实现的准确率的加权平均值，并将前一个阈值的召回率增加用作权重：

$$
AP  = \textstyle\sum_n(R_n - R_{n-1}) P_n
$$

其中 P~n~ 和 R~n~ 是第 n 个阈值的准确率和召回率。一对 (R~k~,P~k~) 被称为 *工作点* ( operating point )。

AP 和操作点下的梯形面积 ([sklearn.metrics.auc](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.auc.html#sklearn.metrics.auc)) 是总结导致不同结果的精确召回曲线的常用方法。在[用户指南](https://scikit-learn.org/stable/modules/model_evaluation.html#precision-recall-f-measure-metrics)中阅读更多内容。

 `准确率-召回率`  曲线通常用于二元分类以研究分类器的输出。为了将 准确率-召回率 曲线和平均精度扩展到多类或多标签分类，需要对输出进行二值化。每个标签可以绘制一条曲线，但也可以通过将标签指标矩阵的每个元素视为二元预测（微平均 - micro-averaging）来绘制准确召回曲线。
> 提示  也可以参考
> [sklearn.metrics.average_precision_score，](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html#sklearn.metrics.average_precision_score)
> [sklearn.metrics.recall_score,](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html#sklearn.metrics.recall_score)
> [sklearn.metrics.precision_score,](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html#sklearn.metrics.precision_score)
> [sklearn.metrics.f1_score](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html#sklearn.metrics.f1_score)


## 在二分问题内

#### 数据集和模型

我们将使用线性 SVC 分类器来区分两种类型的鸢尾花。

```python
import numpy as np
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split

X, y = load_iris(return_X_y=True)

# 加入噪声特征
random_state = np.random.RandomState(0)
n_samples, n_features = X.shape
X = np.concatenate([X, random_state.randn(n_samples, 200 * n_features)], axis=1)

# 限制为前两个类别，然后拆分训练集跟测试集
X_train, X_test, y_train, y_test = train_test_split(
    X[y < 2], y[y < 2], test_size=0.5, random_state=random_state
)

```

线性 SVC 期望每个特征具有相似的值范围。因此，我们将首先使用 [StandardScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html#sklearn.preprocessing.StandardScaler) 缩放数据。

```python
from sklearn.pipeline import make_pipeline
from sklearn.preprocessing import StandardScaler
from sklearn.svm import LinearSVC

classifier = make_pipeline(StandardScaler(), LinearSVC(random_state=random_state))
classifier.fit(X_train, y_train)
```


#### 绘制  准确率-召回率 曲线

要绘制  `准确率-召回率` 曲线，您应该使用 [PrecisionRecallDisplay](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.PrecisionRecallDisplay.html#sklearn.metrics.PrecisionRecallDisplay)。实际上，有两种方法可用，具体取决于您是否已经计算了分类器的预测。

让我们首先绘制没有分类器预测的 `准确率-召回率` 曲线。我们使用 [from_estimator](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.PrecisionRecallDisplay.html#sklearn.metrics.PrecisionRecallDisplay.from_estimator) 在绘制曲线之前为我们计算预测。


```python
from sklearn.metrics import PrecisionRecallDisplay

display = PrecisionRecallDisplay.from_estimator(
    classifier, X_test, y_test, name="LinearSVC"
)
_ = display.ax_.set_title("2-class Precision-Recall curve")
```

![在这里插入图片描述](/photo/InPost/N-3.png)

如果我们已经获得了模型的估计概率或分数，那么我们可以使用 [from_predictions](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.PrecisionRecallDisplay.html#sklearn.metrics.PrecisionRecallDisplay.from_predictions)。

```python
y_score = classifier.decision_function(X_test)

display = PrecisionRecallDisplay.from_predictions(y_test, y_score, name="LinearSVC")
_ = display.ax_.set_title("2-class Precision-Recall curve")
```

![在这里插入图片描述](/photo/InPost/N-4.png)
> 译者注：
> 其实第一个就是把训练好的模型直接扔进去让他们帮我们把测试集算分，
>                第二个是把分数算好直接绘制。

## 在输出结果为多标签模型中

`准确率-召回率` 曲线不支持结果为多标签（多分类）模型。但是，人们可以决定如何处理这种情况。我们在下面展示了这样一个例子。

#### 创建多标签数据、拟合和预测

我们创建了一个多标签数据集，以说明多标签模型中的`准确率-召回率`。

```python
from sklearn.preprocessing import label_binarize

# 使用 label_binarize 进行多标签设置
Y = label_binarize(y, classes=[0, 1, 2])
n_classes = Y.shape[1]

# 分为训练集和测试集
X_train, X_test, Y_train, Y_test = train_test_split(
    X, Y, test_size=0.5, random_state=random_state
)
```

我们使用 [OneVsRestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsRestClassifier.html#sklearn.multiclass.OneVsRestClassifier) 进行多标签预测。

```python
from sklearn.multiclass import OneVsRestClassifier

classifier = OneVsRestClassifier(
    make_pipeline(StandardScaler(), LinearSVC(random_state=random_state))
)
classifier.fit(X_train, Y_train)
y_score = classifier.decision_function(X_test
```

#### 多标签模型中的平均准确率分数

```python
from sklearn.metrics import precision_recall_curve
from sklearn.metrics import average_precision_score

# 对应每个分类（标签）
precision = dict()
recall = dict()
average_precision = dict()
for i in range(n_classes):
    precision[i], recall[i], _ = precision_recall_curve(Y_test[:, i], y_score[:, i])
    average_precision[i] = average_precision_score(Y_test[:, i], y_score[:, i])

# "micro-average"（微平均）: 联合量化所有分类（标签）的分数
precision["micro"], recall["micro"], _ = precision_recall_curve(
    Y_test.ravel(), y_score.ravel()
)
average_precision["micro"] = average_precision_score(Y_test, y_score, average="micro")
```

#### 绘制 微平均 （micro-average） 准确率-召回率 曲线

![在这里插入图片描述](/photo/InPost/N-5.png)

#### 绘制每个类别的 准确率-召回率 曲线和 iso-f1 曲线

```python
import matplotlib.pyplot as plt
from itertools import cycle

# 设置绘图细节
colors = cycle(["navy", "turquoise", "darkorange", "cornflowerblue", "teal"])

_, ax = plt.subplots(figsize=(7, 8))

f_scores = np.linspace(0.2, 0.8, num=4)
lines, labels = [], []
for f_score in f_scores:
    x = np.linspace(0.01, 1)
    y = f_score * x / (2 * x - f_score)
    (l,) = plt.plot(x[y >= 0], y[y >= 0], color="gray", alpha=0.2)
    plt.annotate("f1={0:0.1f}".format(f_score), xy=(0.9, y[45] + 0.02))

display = PrecisionRecallDisplay(
    recall=recall["micro"],
    precision=precision["micro"],
    average_precision=average_precision["micro"],
)
display.plot(ax=ax, name="Micro-average precision-recall", color="gold")

for i, color in zip(range(n_classes), colors):
    display = PrecisionRecallDisplay(
        recall=recall[i],
        precision=precision[i],
        average_precision=average_precision[i],
    )
    display.plot(ax=ax, name=f"Precision-recall for class {i}", color=color)

# 加入 iso-f1 曲线的图例
handles, labels = display.ax_.get_legend_handles_labels()
handles.extend([l])
labels.extend(["iso-f1 curves"])
# 设置图例与坐标轴
ax.set_xlim([0.0, 1.0])
ax.set_ylim([0.0, 1.05])
ax.legend(handles=handles, labels=labels, loc="best")
ax.set_title("Extension of Precision-Recall curve to multi-class")

plt.show()

```

![在这里插入图片描述](/photo/InPost/N-6.png)

源代码如下：
Download Python source code: [plot_precision_recall.py](https://scikit-learn.org/stable/_downloads/98161c8b335acb98de356229c1005819/plot_precision_recall.py)

Download Jupyter notebook: [plot_precision_recall.ipynb](https://scikit-learn.org/stable/_downloads/764d061a261a2e06ad21ec9133361b2d/plot_precision_recall.ipynb)