# 机器学习-视频学习系列12-聚类

## 聚类

### 基于对象的聚类(Objective based clustering)

#### K-means 聚类

- k-means 问题描述: 找到cpt1,cpt2,....cpt, 使 $ min\sum_{i=1}^n m_{j \in 1 \cdots k} d^2(x^i,c_j) $
- k-median 问题描述: 找到cpt1,cpt2,....cpt, 使 $ min\sum_{i=1}^n m_{j \in 1 \cdots k} d(x^i,c_j) $
- k-center 问题描述: 找到一个分类方式最小化最大半径

#### Lloyds method: 总是收敛的(目标函数单调递减,并且有下界, 该方法一定收敛)
问题描述:
> Input: 一组数据集 $ x^1, x^2, \cdots, x^n \in R^d$
Initialize:
    1. 中心点 $c_1, c_2, \cdots, c_k \in R^d$
    2. 簇$C_1,C_2,\cdots,C_k$以随机方式聚合
Repeat:直到目标函数没有任何变化
    1. for each j: $C_j \in \{x \in S \}$ where closest center is $c_j$
    2. for each j: $c_j \in avg(c_j)$

- 初始化很重要(决定了收敛的快慢和输出的质量), 常用的有三种:
1. 随机挑选中心化点. performance 比较差. 随着k的增大, 效果指数级下降
对于k个高斯分布来说, 每个初始化的中心正好在一个不同的高斯分布的概率$\approx\frac {k!}{k^k}\approx\frac1{e^k}$
2. 最远遍历(Furthest Point Heuristic)
先固定一个$c_j$,然后在后续每次取中心点的时候, 都评估距离已经选出来的点最远的点作为当前的中心点. 以此类推, 选出k个中心点. 解决了高斯分布的问题, 但是可能对噪声(outlier)特别敏感
3. K-means++ (效果好, 效率快): 基本思路$D^{\alpha}$抽样
理论上 K-means++ 会得到O(logk)的最优解.
如果$\alpha$=0, 就是随机抽样
如果$\alpha$=$\infin$, 就是最远点初始化
如果$\alpha$=2, k-means++

- K-means++/Lloyd's 运行的时间复杂度
K-means++ 初始化: O(nkd) ,其中n是样本点个数,k是聚类个数, d是样本维度

- 如何选择k
1. 交叉验证
2. Elbow's method. 找到不同k之间的损失函数下降最快的地方. 
3. 尝试层次聚类

### 层次聚类(Hierarchical clustering)

主要讲由下至上的层次聚类
1:34:16 to be continue

### K-means 代码分析及实现

