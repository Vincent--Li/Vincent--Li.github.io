# 机器学习-视频学习系列14-贝叶斯网络


### 机器学习的四种paradigms

1. 连接主义(connectionist) aka 深度学习,神经网络. 认为表示学习非常重要. 需要学出来一个分布式的表示(distributed  representation). Tensorflow PyTorch Theano Caffe
2. 符号主义 Prolog 基于逻辑的学习
3. 统计学习 基于统计学的基本假设 SVM, 统计学习理论, vc-dimention
4. 概率图模型 GAN VAE

### 贝叶斯公式

$P(A|B)=\frac{P(B|A) * P(A)}{P(B)}$

### 概率图模型

以高效的方法求变量的联合分布

{{<mermaid>}}
graph TD
  Earthquake(Earthquake)-->Radio(Radio)
  Earthquake-->Alarm(Alarm)
  Burglary(Burglary)-->Alarm
  Alarm-->Call(Call)
{{</mermaid>}}

1. DAG(Directed Acyclic Graph) 有向无环图
Node-随机变量, Edges-边

2. 上帝视角的P(B,E,A,R,C)
$X_i$和$X_{ancestors}|X_{parents}$独立
$P(B,E,A,R,C)$
$=P(B)P(E|B)P(A|B,E)P(R|A,B,E)P(C|R,A,B,E)$
$=P(B)P(E)P(A|B,E)P(R|E)P(C|A)$

3. DAG能够告诉我们
如果我们有n个变量$x_i$,那么这些变的联合概率分布可以拆解为:
$P(X_{1:n})=P(x_1,x_2,\cdots,x_n)=\prod_{i=1}^nP(x_i|parents(x_i))$

### 概率图模型求法
概率图模型的求法: 消元法 aka(动态规划) aka(分配律)
动态规划的思想在: 概率图模型, HMM, VC-dimension, RL中的MDP, 神经网络BP中的DP 这几块用过
