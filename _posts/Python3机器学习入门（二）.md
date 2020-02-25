---
layout: post
title: 'Python3机器学习入门（二）'
subtitle: '已更新完k近邻算法部分'
date: 2020-02-25
categories: 技术
tags: Python3 机器学习
cover: 'https://cdn.jsdelivr.net/gh/BA-NANA/ba-nana.github.io@1.0/assets/img/background-picture/Python3机器学习入门/p2.png'
comments: true
toc: true
---



## 一.k近邻算法——基础的分类算法

* 这个算法的好处是比较简单，分类识别效果较好

* 假设现在有两组数据

  ~~~python
  raw_data_X = [[3.39,2.33],	#横坐标为肿瘤大小，纵坐标为时间
               [3.11,1.78],
               [1.34,3.36],
               [3.58,4.68],
               [2.28,2.87],
               [7.42,4.69],
               [5.74,3.53],
               [9.17,2.51],
               [7.79,3.42],
               [7.93,0.79]]
  raw_data_y = [0,0,0,0,0,1,1,1,1,1]	#0为良性肿瘤，1为恶性肿瘤
  
  #接收这两组数据，并绘散点图
  x_train = np.array(raw_data_X)
  y_train = np.array(raw_data_y)
  
  plt.scatter(x_train[y_train==0,0],x_train[y_train==0,1],color="g")
  plt.scatter(x_train[y_train==1,0],x_train[y_train==1,1],color="r")
  plt.show()
  ~~~

  <img src="/Users/ming/Documents/GitHub/BA-NANA.github.io/assets/img/Python3机器学习入门2/p1-模拟肿瘤数据.png" style="zoom:38%;" />

* 先假设有一个新的数据，要判断其是不是恶性肿瘤

  ~~~python
  x = np.array([8.09, 3.36])	#新的肿瘤数据
  
  #将上面新的数据加入散点图中观察
  plt.scatter(x_train[y_train==0,0],x_train[y_train==0,1],color="g")
  plt.scatter(x_train[y_train==1,0],x_train[y_train==1,1],color="r")
  plt.scatter(x[0],x[1],color="b")
  plt.show()
  ~~~

  <img src="/Users/ming/Documents/GitHub/BA-NANA.github.io/assets/img/Python3机器学习入门2/p2-模拟肿瘤数据2.png" style="zoom:38%;" />

* 很明显，新增的蓝色点属于恶性肿瘤，怎么通过k近邻算法得到呢？

* 即：新增点与哪k个点最近，用欧拉公式简单得到

  ~~~python
  from math import sqrt
  distances = [sqrt(np.sum((x_train-x)**2)) for x_train in x_train]
  #现在新增点与所有点的距离都保存在distances数组
  
  nearest = np.argsort(distances)	#将点用索引排序
  k = 6	#假设k=6
  topK_y = [y_train[i] for i in nearest[:k]]	#获得前k个点对应的肿瘤分类
  
  from collections import Counter
  votes = Counter(topK_y)	#统计票数 这里结果为 Counter({1: 5, 0: 1})
  votes.most_common(1)	#获得前1个票数最多的[(key,value)]
  predict_y = votes.most_common(1)[0][0]	#取得key，即肿瘤分类
  #结果为1，即新增点被预测为恶性肿瘤
  ~~~

* 调用sckil-learn中的kNN方法

  ~~~python
  from sklearn.neighbors import KNeighborsClassifier
  kNN_classifier = KNeighborsClassifier(n_neighbors=6)	#传入k值
  kNN_classifier.fit(x_train,y_train)
  X_predict = x.reshape(1,-1)
  y_predict = kNN_classifier.predict(X_predict)	#要传入一个二维数组
  y_predict[0]	#获得结果
  ~~~

* 外部写一个高仿版sckil-learn的kNN算法

  ~~~python
  class KNNClassifier:
  
      def __init__(self, k):
  
          assert k >=1, "k must be valid"
          self.k = k
          self._X_train = None
          self._y_train = None
  
      def fit(self, X_train, y_train):
  
          self._X_train = X_train
          self._y_train = y_train
          return self
  
      def predict(self, X_predict):
  
          y_predict = [self._predict(x) for x in X_predict]
  
          return np.array(y_predict)
  
      def _predict(self, x):
          distances = [sqrt(np.sum((x_train - x) ** 2)) for x_train in self._X_train]
          nearest = np.argsort(distances)
  
          topK_y = [self._y_train[i] for i in nearest[:self.k]]
          votes = Counter(topK_y)
  
          return votes.most_common(1)[0][0]
  
      def __repr__(self):
          return "kNN(k=%d)" % self.k
  
  ~~~


### 训练与测试数据集

* 取测试数据，两个关系矩阵要同时乱序

  ~~~python
  #乱序索引即可,自行实现一个train_test_split
  import numpy as np
  
  
  def train_test_split(X, y, test_radio=2, seed=None):
  
      assert X.shape[0] == y.shape[0],\
          "the size of X must be equal to the size of y"
      assert 0.0 <= test_radio <= 1.0,\
          "test_ration must be valid"
  
      if seed:
          np.random.seed(seed)
  
      shuffled_indexes = np.random.permutation(len(X))
  
      test_size = int(len(X)*test_radio)
      test_indexes = shuffled_indexes[:test_size]
      train_indexes = shuffled_indexes[test_size:]
  
      X_train = X[train_indexes]
      y_train = y[train_indexes]
  
      X_test = X[test_indexes]
      y_test = y[test_indexes]
  
      return X_train, X_test, y_train, y_test;
  ~~~

* 测试算法准确率

  ~~~
  sum(y_predict == y_test)
  ~~~

* 使用sklearn中的train_test_split

  ~~~python
  from sklearn.model_selection import train_test_split
  
  X_train, X_test, y_train, y_test = train_test_split(x,y,test_size=0.2)	
  #这里0.2指的是前20%作为训练集，后80%作为测试集
  #将获得的X_train和y_train作为模型，X_test放入写好的算法测试，得到y_predict与y_test比对正确率
  ~~~
### 超参数

* 超参数：运行机器学习前要指定的参数，例如kNN中的k

* 模型参数：算法过程中学习的参数

* 下面写一个 *网格搜索法* 寻找最好的k

* 要考虑平票问题，加上距离权重即可

  ~~~python
for method in ["uniform","distance"]:
      for k in range(1,100):
          knn_clf = KNeighborsClassifier(n_neighbors=k, weights=method)
  ~~~

* 距离：欧拉距离、曼哈顿距离 拓展为 明可夫斯基距离（新的超参数p）

  <img src="/Users/ming/Documents/GitHub/BA-NANA.github.io/assets/img/Python3机器学习入门2/p3-明可夫斯基距离.png" style="zoom:33%;" />

* 最后程序运行结果如下图（考虑了3个超参数：p / method / k）

  <img src="/Users/ming/Documents/GitHub/BA-NANA.github.io/assets/img/Python3机器学习入门2/p4-超参数运行结果.png" style="zoom:50%;" />

* 更多距离定义

  * 向量空间余弦相似度 Cosine Similarity
  * 调整余弦相似度 Adjusted Cosine Similarity
  * 皮尔森相关系数 Pearson Correlation Coefficient
  * Jaccard相似系数 Jaccard Coefficient

### 网格搜索法 Grid Search

* 首先指定超参数取值范围

  ~~~python
  param_grid = [
      {
          'weights':['uniform'],
          'n_neighbors':[i for i in range(1,11)]
      },
      {
          'weights':['distance'],
          'n_neighbors':[i for i in range(1,11)]
          'p':[i for i in range(1,6)]
      }
  ]
  ~~~

* 进行搜索

  ~~~python
  knn_clf = KNeighborsClassifier()
  
  #CV是交叉验证的意思
  from sklearn.model_selection import GridSearchCV
  
  #n_jobs代表用计算机的核数，-1位全部
  #verbose代表输出信息，数值越大越详细
  grid_search = GridSearchCV(knn_clf, param_grid, n_jobs=-1, verbose=2)
  
  grid_search.fit(X_train,y_train)
  
  #最佳分类器
  grid_search.best_estimator_
  #分类器正确率
  grid_search.best_score_
  ~~~


### 数据归一化

* 将所有数据映射到同一尺度中

* 最值归一化：把所有数据映射到0~1间，适用于有明显边界的情况
  $$
  Xscale = (X-Xmin)/(Xmax-Xmin)
  $$

* 均值方差归一化：所有数据归一到均值为0方差为1的分布中，适用于数据分布没有明显边界 (S为方差)
  $$
  Xscale = (X-Xmean)/S
  $$
  
* 使用StandardScaler

  ~~~python
  from sklearn.preprocessing import StandardScaler
  standardScaler = StandardScaler()
  standardScaler.fit(X_train)
  
  #得到每列的均值
  standardScaler.mean_
  #得到每列的方差
  standardScaler.scale_
  
  #用transform进行均值方差归一化
  X_train = standardScaler.transform(X_train)
  X_test_standard = standardScaler.transform(X_test)
  ~~~

* 自行实现一个StandardScaler

  ~~~python
  import numpy as np
  
  class StandardScaler:
  
      def __init__(self):
          self.mean_ = None
          self.scale_ = None;
  
      def fit(self, X):
          """根据训练数据集获得数据均值与方差"""
          assert X.ndim == 2,"The dimension of X must be 2"
  
          self.mean_ = np.array([np.mean(X[:,i]) for i in range(X.shape[1])])
          self.scale_ = np.array([np.std(X[:, i]) for i in range(X.shape[1])])
  
          return self
  
      def transform(self,X):
          """根据X进行均值方差归一化"""
          assert X.ndim == 2, "The dimension of X must be 2"
          assert self.mean_ is not None and self.scale_ is not None, \
              "must fit before transform!"
          assert X.shape[1] == len(self.mean_), \
              "The feature number of X must be equal to mean_ and std_"
  
          resX = np.empty(shape=X.shape,dtype=float)
          for col in range(X.shape[1]):
              resX[:,col] = (X[:,col] - self.mean_[col]) / self.scale_[col]
  
          return resX
  ~~~

  正在更新...

------

