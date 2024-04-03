---
layout: post
title:  基于模型和顺序特征选择 - Model-based and sequential feature selection
date:   2021-10-06 22:27:09 +0800
categories: 更无聊的人生
author: 崔秉龙
location: Guangzhou, China
---

# Model-based and sequential feature selection

## 基于模型和顺序特征选择


> 本文翻译自 [Model-based and sequential feature selection](https://scikit-learn.org/stable/auto_examples/feature_selection/plot_select_from_model_diabetes.html#sphx-glr-auto-examples-feature-selection-plot-select-from-model-diabetes-py)

此示例说明并比较了两种特征选择方法：基于特征重要性的 [SelectFromModel](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.SelectFromModel.html#sklearn.feature_selection.SelectFromModel) 和依赖于贪婪方法的 `SequentialFeatureSelection`。

我们使用了有442个病人的数据，其中每个病人有10个特征值。


Authors: Manoj Kumar (mks542@nyu.edu) , [Maria Telenczuk](https://github.com/maikia), Nicolas Hug.

License: BSD 3 clause

```python
print(__doc__)
```
### 读取数据
我们首先加载 scikit-learn 中可用的糖尿病数据集，并展示其描述：

```python
from sklearn.datasets import load_diabetes

diabetes = load_diabetes()
X, y = diabetes.data, diabetes.target
print(diabetes.DESCR)
```

```
.. _diabetes_dataset:

Diabetes dataset
----------------

在基准时间上测量的十个变量：年龄、性别、体重指数、平均血压，
以及442名糖尿病患者，在基准时间后一年疾病进展的定量测量。


**Data Set Characteristics:**

  :Number of Instances: 442

  :Number of Attributes: First 10 columns are numeric predictive values

  :Target: Column 11 is a quantitative measure of disease progression one year after baseline

  :Attribute Information:
      - age     age in years
      - sex
      - bmi     body mass index
      - bp      average blood pressure
      - s1      tc, total serum cholesterol
      - s2      ldl, low-density lipoproteins
      - s3      hdl, high-density lipoproteins
      - s4      tch, total cholesterol / HDL
      - s5      ltg, possibly log of serum triglycerides level
      - s6      glu, blood sugar level

Note: Each of these 10 feature variables have been mean centered and scaled by the standard deviation times `n_samples` (i.e. the sum of squares of each column totals 1).

Source URL:
https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html

For more information see:
Bradley Efron, Trevor Hastie, Iain Johnstone and Robert Tibshirani (2004) "Least Angle Regression," Annals of Statistics (with discussion), 407-499.
(https://web.stanford.edu/~hastie/Papers/LARS/LeastAngle_2002.pdf)
```
### 重要特征的系数

为了了解特征的重要性，我们将使用 [LassoCV](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LassoCV.html#sklearn.linear_model.LassoCV) 估计器。具有最高绝对 coef_ 值的特征被认为是最重要的。我们可以直接观察系数而无需缩放它们（或缩放数据），因为从上面的描述中，我们知道这些特征已经标准化了。有关线性模型系数解释的更完整示例，您可以参考[线性模型系数解释中的常见缺陷](https://scikit-learn.org/stable/auto_examples/inspection/plot_linear_model_coefficient_interpretation.html#sphx-glr-auto-examples-inspection-plot-linear-model-coefficient-interpretation-py)。

```python
import matplotlib.pyplot as plt
import numpy as np
from sklearn.linear_model import LassoCV

lasso = LassoCV().fit(X, y)
importance = np.abs(lasso.coef_)
feature_names = np.array(diabetes.feature_names)
plt.bar(height=importance, x=feature_names)
plt.title("Feature importances via coefficients")
plt.show()
```

![在这里插入图片描述](/photo/InPost/N-2.png)

### 根据重要性选择特征

假设现在我们要根据系数选择最重要的两个特征。 [SelectFromModel](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.SelectFromModel.html#sklearn.feature_selection.SelectFromModel) 仅用于此目的。 [SelectFromModel](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.SelectFromModel.html#sklearn.feature_selection.SelectFromModel) 接受`阈值`参数并将选择重要性（由系数定义）高于此阈值的特征。

由于我们只想选择 2 个特征，因此我们将此阈值设置为略高于第三个最重要特征的系数。

```python
from sklearn.feature_selection import SelectFromModel
from time import time

threshold = np.sort(importance)[-3] + 0.01

tic = time()
sfm = SelectFromModel(lasso, threshold=threshold).fit(X, y)
toc = time()
print("Features selected by SelectFromModel: "
      f"{feature_names[sfm.get_support()]}")
print(f"Done in {toc - tic:.3f}s")
```

```
Features selected by SelectFromModel: ['s1' 's5']
Done in 0.048s
```

### 使用顺序特征选择选择特征

另一种选择特征的方法是使用 [SequentialFeatureSelector](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.SequentialFeatureSelector.html#sklearn.feature_selection.SequentialFeatureSelector) (SFS)。 SFS 是一个贪婪的过程，在每次迭代中，我们根据交叉验证分数选择最好的新特征添加到我们选择的特征中。也就是说，我们从 0 个特征开始，选择得分最高的最好的单个特征。重复该过程，直到我们达到所需的所选特征数量。

我们也可以反其道而行（backward SFS），即从所有特征开始，贪婪地选择特征一一去除。我们在这里说明了这两种方法。

```python
from sklearn.feature_selection import SequentialFeatureSelector

tic_fwd = time()
sfs_forward = SequentialFeatureSelector(lasso, n_features_to_select=2,
                                        direction='forward').fit(X, y)
toc_fwd = time()

tic_bwd = time()
sfs_backward = SequentialFeatureSelector(lasso, n_features_to_select=2,
                                         direction='backward').fit(X, y)
toc_bwd = time()

print("Features selected by forward sequential selection: "
      f"{feature_names[sfs_forward.get_support()]}")
print(f"Done in {toc_fwd - tic_fwd:.3f}s")
print("Features selected by backward sequential selection: "
      f"{feature_names[sfs_backward.get_support()]}")
print(f"Done in {toc_bwd - tic_bwd:.3f}s")
```

```
Features selected by forward sequential selection: ['bmi' 's5']
Done in 3.271s
Features selected by backward sequential selection: ['bmi' 's5']
Done in 9.821s
```

### 讨论

有趣的是，前向和后向选择选择了相同的特征集。一般来说，情况并非如此，两种方法会导致不同的结果。

我们还注意到，SFS 选择的特征与特征重要性选择的特征不同：SFS 选择 `bmi` 而不是 `s1`。不过，这听起来确实合理，因为根据系数，`bmi` 对应于第三个最重要的特征。考虑到 SFS 根本不使用这些系数，这是非常了不起的。

最后，我们应该注意到 [SelectFromModel](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.SelectFromModel.html#sklearn.feature_selection.SelectFromModel) 比 SFS 快得多。事实上，[SelectFromModel](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.SelectFromModel.html#sklearn.feature_selection.SelectFromModel) 只需要拟合一个模型一次，而 SFS 需要为每次迭代交叉验证许多不同的模型。然而，SFS​​ 适用于任何模型，而 [SelectFromModel](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.SelectFromModel.html#sklearn.feature_selection.SelectFromModel) 需要底层估算器来公开 `coef_` 属性或 `feature_importances_` 属性。前向 SFS 比后向 SFS 更快，因为它只需要执行 `n_features_to_select = 2` 次迭代，而后向 SFS 需要执行 `n_features - n_features_to_select = 8` 次迭代。

源代码如下：
Download Python source code: [plot_select_from_model_diabetes.py](https://scikit-learn.org/stable/_downloads/53e76f761ef04e8d06fa5757554513b0/plot_select_from_model_diabetes.py)

Download Jupyter notebook: [plot_select_from_model_diabetes.ipynb](https://scikit-learn.org/stable/_downloads/f1e887db7b101f4c858db7db12e9c7e2/plot_select_from_model_diabetes.ipynb)