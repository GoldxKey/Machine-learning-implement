

朴素贝叶斯算法是基于贝叶斯定理和特征之间条件独立假设的分类方法。对于给定的训练数据集，首先基于特征条件独立假设学习输入/输出的联合概率分布；然后基于此模型，对给定的输入x，利用贝叶斯定理求出后验概率最大的输出y。朴素贝叶斯算法实现简单，学习和预测的效率都很高，是一种常用的方法。

本文考虑当特征是离散情形时，朴素贝叶斯分类方法的原理以及如何从零开始实现贝叶斯分类算法。





## 1. 朴素贝叶斯算法原理


### 1.1 贝叶斯定理


根据贝叶斯定理，对一个分类问题，给定样本特征x，样本属于类别y的概率是 

$$ p(y|x) = \frac{p(y)p(x|y)}{p(x)} \,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,(1) $$

在这里，x是一个特征向量，设x维度为n。因为朴素的假设，即特征之间条件独立，根据全概率公式展开，公式(1)可以写成


\begin{align*}
p(y=c_k|x=(x_1, x_2, ..., x_n)) 
& = \frac{p(y=c_k)p(x|y=c_k)}{p(x)}  \\
& = \frac{p(y=c_k)p(x=(x_1, x_2, ..., x_n)|y=c_k)}{p(x)}  \\
& = \frac{p(y=c_k)p(x=x_1|y=c_k)p(x=x_2|y=c_k)...p(x=x_n|y=c_k)}{p(x)}  \\
& = \frac{p(y=c_k) \prod_{i=1}^{n} p(x=x_i|y=c_k)}{p(x)}  \\
& = \frac{p(y=c_k) \prod_{i=1}^{n} p(x=x_i|y=c_k)} { \sum_{k=1}^{n_{labels}} p(x|y=c_k)p(y=c_k)}  \\
& = \frac{p(y=c_k) \prod_{i=1}^{n} p(x=x_i|y=c_k)} { \sum_{k=1}^{n_{labels}} p(y=c_k)\prod_{i=1}^{n} p(x=x_i|y=c_k)}   \,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\, (2)
\end{align*}



上式中$p(y=c_k)$称为先验概率，$ { \prod_{i=1}^{n} p(x=x_i|y=c_k)}  $称为似然度，$p(y=c_k|x=(x_1, x_2, ..., x_n)) $称为后验概率，显然，给定一个数据集，先验概率和似然度我们都可以计算出来，因此，后验概率 $p(y=c_k|x=(x_1, x_2, ..., x_n)) ( k = 1, 2, ..., n_{labels})$ 就可以得出，然后找出$\{ p(y=c_1|x=(x_1, x_2, ..., x_n)), p(y=c_2|x=(x_1, x_2, ..., x_n)) , ..., p(y=c_{n_{labels}}|x=(x_1, x_2, ..., x_n)) \}$中的最大值，其所对应的$c_k$就是算法预测的类别。





### 1.2 朴素贝叶斯算法的参数学习



下面介绍如何从数据中，学习得到朴素贝叶斯分类模型的参数。


设训练集$X_{mxn}=\{(x^{(1)},y^{(1)}),(x^{(2)},y^{(2)}), ..., (x^{(m)},y^{(m)})\} $中有$m$个样本，其中 $x^{(i)}=(x^{(i)}_1,x^{(i)}_2,...,x^{(i)}_n)^T$是$n$维向量，$y^{(i)}∈\{c_1,c_2,...c_{n_{labels}}\}$属于$c_{n_{labels}}$类中的一类。

首先，计算公式(2)中的$p(y=c_k)$, 
$$p(y=c_k)=\sum_i^m \frac{I(y^{(i)}=c_k)}{m}  \,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\, （3）$$
其中$I(x)$为指示函数，若括号内成立，则计1，否则为0。

接下来计算分子中的条件概率，设$n$维特征的第$j$维有$L$个取值，则第$j$维特征的某个取值$a_{jl}$，在给定某分类$c_k$下的条件概率为：
        $$ p(x_j=a_{jl}|y=c_k)=\frac{\sum_i^m=I(x_{ji}=a_{jl},y^{(i)}=c_k)}{ \sum_i^m I(y^{(i)}=c_k)} \,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,（4）$$

经过上述两个步骤，我们就可以得到模型的基本概率，也就完成了学习的任务。




### 1.3 朴素贝叶斯算法的分类


通过学到的概率，给定未分类新实例$x$，就可以通过上述概率进行计算，得到该实例属于各类的后验概率$p(y=c_k|x)$，因为对所有的类来说，公式(2)中分母的值都相同，所以只计算分子部分即可，具体步骤如下：

1.计算该实例属于$y=c_k$类的概率
$$p(y=c_k|x)=p(y=c_k)\prod_{i=1}^{n} p(x=x_i|y=c_k) \,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,(5)   $$

确定该实例所属的类别标签$y$
$$y=arg \, \underset{c_k}{ max } p(y=c_k|x) \,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\, (6)  $$

这样我们就得到了新实例的分类结果。



## 2. 朴素贝叶斯算法的应用


前面讲了一大堆的公式，为了让大家更好的理解朴素贝叶斯算法，下面从一个具体的例子开始讲起，你会看到贝叶斯分类器非常简单。

现在有一个数据集，总共5个样本，如下表。目标是预测用户是否会转化。


    id 　　性别　　 城市　　　转化

    1  　 　M　    北京　　　1

    2  　 　F　    上海　　　0

    3   　　M　　  广州　    1

    4  　 　M　　  北京　    1

    5  　 　F　    上海　　　0


假设我们现在需要预测一个用户(上海男性)是否会转化？

根据贝叶斯定理：

    　P(A|B) = P(B|A) P(A) / P(B)

可得

    　　　P(转化|Mx上海)
    　　　　= P(Mx上海|转化) x P(转化) / P(Mx上海)

假定"性别"和"城市"这两个特征是独立的，因此，上面的等式就变成了

    　　　P(转化|Mx上海)
    　　　　= (P(M|转化) x P(上海|转化) x P(转化)) / (P(M) x P(上海))
    　　　　= (2/2 x 0/2 x 2/5) / (P(M) x P(上海))
    　　　　= 0

因此，这个上海男性用户转化的概率是0。同理，可以计算这个这个上海男性用户不转化的概率，即

    　　　P(不转化|Mx上海)
    　　　　= (P(M|不转化) x P(上海|不转化) x P(不转化)) / (P(M) x P(上海))
    　　　　= (1/3 x 2/3 x 3/5) / (P(M) x P(上海))

从上面的计算结果可以看出，分母都一样，因此，我们只需要比价分子的大小即可。显然，P(不转化|Mx上海)的分子大于P(转化|Mx上海)的分子，因此，这个上海男性用户的预测结果是不转化。这就是贝叶斯分类器的基本方法：在统计资料的基础上，依据某些特征，计算各个类别的概率，从而实现分类。



## 3. 朴素贝叶斯算法的注意事项

上一节(第2节)可以看出，P(转化|Mx上海)=0，也就是说，我们得到了‘上海男人不转化’这个结论，显然这是不合理的，之所以出现了这样不合理的结果，是因为我们的训练集中没有上海男人有过转化，也许是我们的数据集收集太少或者其它原因导致的。所以我们应该对我们的结果进行修正。通常的修正方法是用拉普拉斯平滑方法。

### 3.1 拉普拉斯平滑


拉普拉斯平滑思想非常简单，其就是给学习步骤中的两个概率计算公式(3)(4)中的分子和分母都分别加上一个常数，就可以避免这个问题。更新过后的公式如下：

$$p(y=c_k)=\sum_i^m \frac{I(y^{(i)}=c_k) + \lambda}{m + \lambda n_{labels}}  \,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\, （7）$$

其中，$n_{labels}$是类别标签的个数，$\lambda \geq 0$是一个常数。

$$ p(x_j=a_{jl}|y=c_k)=\frac{\sum_i^m=I(x_{ji}=a_{jl},y^{(i)}=c_k) + \lambda}{ \sum_i^m I(y^{(i)}=c_k) + \lambda L_j} \,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\,\, \,\,\,\,\,\,\,\,\,\,\,（8）$$

其中，$L_j$是$X[y==c_k]$数据集中第$j$维特征的不同特征值的个数，用$Python$脚本来描述，则$L_j=len(np.unique(X[y==c_k][:, j]))$。

显然，对于任意的$j=1,2,..., L_j, \, k=1,2,...,n_{labels}$，有
$$ p(x_j=a_{jl}|y=c_k)>0, \,\,\,\,\,\, \sum_{l=1}^{L_j} p(x_j=a_{jl}|y=c_k)=1 $$


这说明(8)式仍然是概率，同理可知(7)式也是概率。平滑因子λ=0即为(3)(4)实现的最大似然估计，这时会出现前文提到的0概率问题；而λ=1则避免了0概率问题，这种方法被称为拉普拉斯平滑。

下面，我们就用拉普拉斯平滑修正第2节中的实例：


    　　　P(转化|Mx上海)
    　　　　= (P(M|转化) x P(上海|转化) x P(转化)) / (P(M) x P(上海))
    　　　　= (3/3 x 1/4 x 3/7) / (P(M) x P(上海))
        
        
    　　　P(不转化|Mx上海)
    　　　　= (P(M|不转化) x P(上海|不转化) x P(不转化)) / (P(M) x P(上海))
    　　　　= (2/5 x 3/5 x 4/7) / (P(M) x P(上海))
        


### 3.2 概率连乘的问题

在具体的实现代码过程中，我们有一点需要注意，就是很多个大于0且小于1的数累乘最终会接近于0，由于计算机的位数限制，所以多个概率相乘之后的数值在计算机内存中保存的数值是0。针对这个问题，我们通常将概率连乘式子取对数，将累乘的运算转换成累加的运算，这样就避免了上述的问题。



## 4. 朴素贝叶斯算法的优缺点


朴素贝叶斯的主要优点有：

* 朴素贝叶斯模型发源于古典数学理论，有稳定的分类效率。

* 对小规模的数据表现很好，能个处理多分类任务，适合增量式训练，尤其是数据量超出内存时，我们可以一批批的去增量训练。

* 对缺失数据不太敏感，算法也比较简单，常用于文本分类。


朴素贝叶斯的主要缺点有：

* 理论上，朴素贝叶斯模型与其他分类方法相比具有最小的误差率。但是实际上并非总是如此，这是因为朴素贝叶斯模型假设属性之间相互独立，这个假设在实际应用中往往是不成立的，在属性个数比较多或者属性之间相关性较大时，分类效果不好。而在属性相关性较小时，朴素贝叶斯性能最为良好。对于这一点，有半朴素贝叶斯之类的算法通过考虑部分关联性适度改进。

* 需要知道先验概率，且先验概率很多时候取决于假设，假设的模型可以有很多种，因此在某些时候会由于假设的先验模型的原因导致预测效果不佳。

* 由于我们是通过先验和数据来决定后验的概率从而决定分类，所以分类决策存在一定的错误率。

* 对输入数据的表达形式很敏感。

* 对于连续特征，假设了每个类别标签的数据集中的每个特征均服从高斯分布。


```python
from __future__ import division, print_function
from sklearn import datasets
import matplotlib.pyplot as plt
import math
import numpy as np
import pandas as pd
%matplotlib inline


def shuffle_data(X, y, seed=None):
    if seed:
        np.random.seed(seed)

    idx = np.arange(X.shape[0])
    np.random.shuffle(idx)

    return X[idx], y[idx]


# 正规化数据集 X
def normalize(X, axis=-1, p=2):
    lp_norm = np.atleast_1d(np.linalg.norm(X, p, axis))
    lp_norm[lp_norm == 0] = 1
    return X / np.expand_dims(lp_norm, axis)


# 标准化数据集 X
def standardize(X):
    X_std = np.zeros(X.shape)
    mean = X.mean(axis=0)
    std = X.std(axis=0)
    
    # 做除法运算时请永远记住分母不能等于0的情形
    # X_std = (X - X.mean(axis=0)) / X.std(axis=0) 
    for col in range(np.shape(X)[1]):
        if std[col]:
            X_std[:, col] = (X_std[:, col] - mean[col]) / std[col]
    return X_std


# 划分数据集为训练集和测试集
def train_test_split(X, y, test_size=0.2, shuffle=True, seed=None):
    if shuffle:
        X, y = shuffle_data(X, y, seed)

    n_train_samples = int(X.shape[0] * (1-test_size))
    x_train, x_test = X[:n_train_samples], X[n_train_samples:]
    y_train, y_test = y[:n_train_samples], y[n_train_samples:]

    return x_train, x_test, y_train, y_test


def accuracy(y, y_pred):
    y = y.reshape(y.shape[0], -1)
    y_pred = y_pred.reshape(y_pred.shape[0], -1)
    return np.sum(y == y_pred)/len(y)




class NaiveBayes():
    """朴素贝叶斯分类模型. """
    def __init__(self):
        self.classes = None
        self.X = None
        self.y = None
        # 存储每个类别标签数据集中每个特征中每个特征值的出现概率, 因为预测的时候需要, 模型训练的过程中其实就是计算出这些概率
        self.parameters = []

    def fit(self, X, y):
        self.X = X
        self.y = y
        self.classes = np.unique(y)
        # 遍历所有类别的数据集，计算每一个类别数据集每个特征中每个特征值的出现概率
        for i in range(len(self.classes)):
            c = self.classes[i]
            # 选出该类别的数据集
            x_where_c = X[np.where(y == c)]
            
            self.parameters.append([])
            # 遍历该类别数据的所有特征，计算该类别数据集每个特征中每个特征值的出现概率
            for j in range(x_where_c.shape[1]):
                feautre_values_where_c_j = np.unique(x_where_c[:, j])
                
                parameters = {}
                # 遍历整个训练数据集该特征的所有特征值(如果遍历该类别数据集x_where_c中该特征的所有特征值, 
                # 则每列的特征值都不全，因此整个数据集X中存在但是不在x_where_c中的特征值将得不到其概率,
                # feautre_values_where_c_j), 计算该类别数据集该特征中每个特征值的出现概率
                for feature_value in X[:, j]: # feautre_values_where_c_j
                    n_feature_value = x_where_c[x_where_c[:, j]==feature_value].shape[0]
                    # 用Laplance平滑对概率进行修正, 并且用取对数的方法将累乘转成累加的形式
                    parameters[feature_value] = np.log((n_feature_value + 1) / 
                                        (x_where_c.shape[0] + len(feautre_values_where_c_j)))
                self.parameters[i].append(parameters)
                
    # 计算先验概率
    def calculate_priori_probability(self, c):
        x_where_c = self.X[np.where(self.y == c)]
        n_samples_for_c = x_where_c.shape[0]
        n_samples = self.X.shape[0]
        return (n_samples_for_c + 1) / (n_samples + len(self.classes))

    def classify(self, sample):
        posteriors = []
        
        # 遍历所有类别
        for i in range(len(self.classes)):
            c = self.classes[i]
            prior = self.calculate_priori_probability(c)
            posterior = np.log(prior)
            
            # probability = P(Y)*P(x1|Y)*P(x2|Y)*...*P(xN|Y)
            # 遍历所有特征
            for j, params in enumerate(self.parameters[i]):
                # 取出预测样本的第j个特征
                sample_feature = sample[j]
                # 取出参数中第i个类别第j个特征特征值为sample_feature的概率, 如果测试集中的样本
                # 有特征值没有出现, 则假设该特征值的概率为1/self.X.shape[0]
                proba = params.get(sample_feature, np.log(1/self.X.shape[0]))
                
                # 朴素贝叶斯模型假设特征之间条件独立，即P(x1,x2,x3|Y) = P(x1|Y)*P(x2|Y)*P(x3|Y)
                posterior += proba
            
            posteriors.append(posterior)
        
        # 对概率进行排序
        index_of_max = np.argmax(posteriors)
        max_value = posteriors[index_of_max]

        return self.classes[index_of_max]

    # 对数据集进行类别预测
    def predict(self, X):
        y_pred = []
        for sample in X:
            y = self.classify(sample)
            y_pred.append(y)
        return np.array(y_pred)


    
def main():
    X = np.array([['M','北京'], ['F', '上海'], ['M' ,'广州'], ['M' ,'北京'], 
                  ['F' ,'上海'], ['M','北京'], ['F', '上海'], ['M' ,'广州'], 
                  ['M' ,'北京'], ['F' ,'上海']])
    y = np.array([1, 0, 1, 1, 0, 1, 0, 1, 1, 0])

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.6)

    clf = NaiveBayes()
    clf.fit(X_train, y_train)
    y_pred = np.array(clf.predict(X_test))

    accu = accuracy(y_test, y_pred)

    print ("Accuracy:", accu)


if __name__ == "__main__":
    main()

```

    Accuracy: 1.0


参考文献：
http://www.ruanyifeng.com/blog/2013/12/naive_bayes_classifier.html

李航《统计学习方法》
