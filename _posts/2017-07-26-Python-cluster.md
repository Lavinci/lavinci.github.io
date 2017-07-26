---
layouts: post
title: "机器学习之聚类"
excerpt: "聚类介绍及简单实例"
date: 2017-07-26 16:14
tags: [Python]
---
# sklearn库
sklearn是scikit-learn的简称，是一个基于Python的第三方模块。
sklearn库集成了一些常用的机器学习方法，在进行机器学习任务时，
并不需要实现算法，只需要简单的调用sklearn库中提供的模块就能
完成大多数的机器学习任务。
sklearn库是在Numpy、Scipy和matplotlib的基础上开发而成的，
因此在介绍sklearn的安装前，需要先安装这些依赖库。

# Numpy库
Numpy（Numerical Python的缩写）是一个开源的Python科学计算库。在
Python中虽然提供了list容器和array模块，但这些结构并不适合于进行数值计算，
因此需要借助于Numpy库创建常用的数据结构（如：多维数组，矩阵等）以及进
行常用的科学计算（如：矩阵运算）。
Scipy库是sklearn库的基础，它是基于Numpy的一个集成了多种数学算法和函
数的Python模块。它的不同子模块有不同的应用，如：积分、插值、优化和信号
处理等。
matplotlib是基于Numpy的一套Python工具包，它提供了大量的数据绘图工
具，主要用于绘制一些统计图形，将大量的数据转换成更加容易被接受的图表。
（注意要先安装numpy再安装matplotlib库）

安装包的下载
(下载地址)(http://www.lfd.uci.edu/~gohlke/pythonlibs/]

安装顺序
安装顺序如下：
. Numpy库
. Scipy库
. matplotlib库
. sklearn库

依赖库之Numpy的安装
访问Numpy的相关下载链接依据Python的具体版本下载对应的文件。


依赖库之Numpy的安装
找到下载的文件的路径，打开windows的DOS命令行窗口，执行如下命令：
pip install "numpy-1.11.3+mkl-cp35-cp35m-win_amd64.whl"

依赖库之Scipy的安装
找到下载的文件的路径，打开windows的DOS命令行窗口，使用如下命令：
pip install scipy-0.19.0-cp35-cp35m-win_amd64.whl

依赖库之matplotlib的安装
找到下载的文件的路径，打开windows的DOS命令行窗口，使用如下命令：
pip install matplotlib-2.0.0-cp35-cp35m-win_amd64.whl

sklearn库的安装
找到下载的文件的路径，打开windows的DOS命令行窗口，使用如下命令：
pip install scikit_learn-0.18.1-cp35-cp35m-win_amd64.whl

>>> import matplotlib
>>> import sklearn
>>> import sklearn

无错误提示就是安装成功
--------------------------------------------
# 聚类

## KMeans
调用KMeans方法所需参数：
• n_clusters：用于指定聚类中心的个数
• init：初始聚类中心的初始化方法
• max_iter：最大的迭代次数
一般调用时只用给出n_clusters即可，init 默认是k-means++，max_iter默认是300
其它参数：
• data：加载的数据
• label：聚类后各数据所属的标签
• axis: 按行求和
• fit_predict()：计算簇中心以及为簇分配序号

[31省统计信息下载地址](https://raw.githubusercontent.com/Lavinci/PicOnNet/master/article/python/machineLearn/31city.txt)

![聚类一](https://raw.githubusercontent.com/Lavinci/PicOnNet/master/article/python/machineLearn/JuLei1.png)
```python
import numpy as np
from sklearn.cluster import KMeans


def loadData(filePath):
    fr = open(filePath,'r+')
    lines = fr.readlines()
    retData = []
    retCityName = []
    for line in lines:
        items = line.strip().split(",")
        retCityName.append(items[0])
        retData.append([float(items[i]) for i in range(1,len(items))])
    return retData,retCityName


if __name__ == '__main__':
    data,cityName = loadData('31city.txt')
    km = KMeans(n_clusters=4)           #分成4簇
    label = km.fit_predict(data)        #为簇分配序号
    expenses = np.sum(km.cluster_centers_,axis=1) #axis按行求和

    #print(expenses)
    CityCluster = [[],[],[],[]]
    for i in range(len(cityName)):
        CityCluster[label[i]].append(cityName[i])

    for i in range(len(CityCluster)):
        print("Expenses:%.2f" % expenses[i])
        print(CityCluster[i])
```

## DBscan

DBSCAN密度聚类
DBSCAN算法是一种基于密度的聚类算法：
• 聚类的时候不需要预先指定簇的个数
• 最终的簇的个数不定

DBSCAN算法将数据点分为三类：
• 核心点：在半径Eps内含有超过MinPts数目的点
• 边界点：在半径Eps内点的数量小于MinPts，但是落在核心点的邻域内
• 噪音点：既不是核心点也不是边界点的点

![聚类二](https://raw.githubusercontent.com/Lavinci/PicOnNet/master/article/python/machineLearn/JuLei2.png)

DBSCAN算法流程：
1.将所有点标记为核心点、边界点或噪声点；
2.删除噪声点；
3.为距离在Eps之内的所有核心点之间赋予一条边；
4.每组连通的核心点形成一个簇；
5.将每个边界点指派到一个与之关联的核心点的簇中（哪一个核心点的半
径范围之内）。

![聚类三](https://raw.githubusercontent.com/Lavinci/PicOnNet/master/article/python/machineLearn/JuLei3.png)
![聚类四](https://raw.githubusercontent.com/Lavinci/PicOnNet/master/article/python/machineLearn/JuLei4.png)
![聚类五](https://raw.githubusercontent.com/Lavinci/PicOnNet/master/article/python/machineLearn/JuLei5.png)
![聚类六](https://raw.githubusercontent.com/Lavinci/PicOnNet/master/article/python/machineLearn/JuLei6.png)

DBSCAN的应用实例 数据介绍：
现有大学校园网的日志数据，290条大学生的校园网使用情况数据，数据包括用户ID，设备的MAC地址，IP地址，开始上网时间，停止上网时间，上网时长，校园网套餐等。利用已有数据，分析学生上网的模式。

实验目的：
通过DBSCAN聚类，分析学生上网时间和上网时长的模式。

[上网时长下载地址](https://raw.githubusercontent.com/Lavinci/PicOnNet/master/article/python/machineLearn/TestData.txt)
样例:
学生上网日志（单条数据格式）
记录编号 2c929293466b97a6014754607e457d68
学生编号 U201215025
MAC地址 A417314EEA7B
IP地址 10.12.49.26
开始上网时间 2014-07-20 22:44:18.540000000
停止上网时间 2014-07-20 23:10:16.540000000
上网时长 1558

DBSCAN主要参数：
eps: 两个样本被看作邻居节点的最大距离
min_samples: 簇的样本数
metric：距离计算方式
例：sklearn.cluster.DBSCAN(eps=0.5, min_samples=5, metric='euclidean')

```python
import numpy as np
import sklearn.cluster as skc
from sklearn import metrics
import matplotlib.pyplot as plt

# 分离数据
mac2id = dict()
onlinetimes=[]
f=open('TestData.txt',encoding='utf-8')
for line in f:
    mac=line.split(',')[2]
    onlinetime=int(line.split(',')[6])
    starttime=int(line.split(',')[4].split(' ')[1].split(':')[0])
    if mac not in mac2id:
        mac2id[mac]=len(onlinetimes)
        onlinetimes.append((starttime,onlinetime))
    else:
        onlinetimes[mac2id[mac]]=[(starttime,onlinetime)]

real_X=np.array(onlinetimes).reshape((-1,2))

X=real_X[:,0:1]

# 开始聚类
db=skc.DBSCAN(eps=0.01,min_samples=20).fit(X)
labels = db.labels_
print('Labels: ')
print(labels)

raito = len(labels[labels[:] == -1]) /len(labels)

print('Noise raito',format(raito,'.2%'))  # 噪点信息

n_clusters_ = len(set(labels)) - (1 if -1 in labels else 0)

print('Estimated number of clusters :%d' % n_clusters_)   # 聚处的数量
print("Silhouette Coefficient: %0.3f"% metrics.silhouette_score(X,labels))

for i in range(n_clusters_):
    print('Cluster ',i,':')
    print(list(X[labels == i].flatten() ))
plt.hist(X,24)    # 绘制直方图
plt.show()
```
![聚类七](https://raw.githubusercontent.com/Lavinci/PicOnNet/master/article/python/machineLearn/JuLei6.png)

详细信息:
https://docs.scipy.org/doc/scipy/reference/generated/scipy.spatial.distance.cdist.html#scipy.spatial.distance.cdist
