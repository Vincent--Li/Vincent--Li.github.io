# 机器学习-视频学习系列15-隐马尔可夫模型


### 问题模型
{{<mermaid>}}
graph LR
  x1(x1)-->x2(x2)
  x2-->x3(x3)
  x3-->xn(xn)
  x1-->y1(y1)
  x2-->y2(y2)
  x3-->y3(y3)
{{</mermaid>}}

### 模型公式
目标: 求$P(x_t|y_{1:t})$
步骤: 
1. predict 求$P(x_t|y_{1:t-1})$
使用到恒等变形公式:
$P(A|BC)$
$= \frac{P(ABC)}{P(BC)}$ 
$= \frac{P(B|AC)P(A|C)P(C)}{P(B|C)P(C)}$ 
$= \frac{P(B|AC)P(A|C)}{P(B|C)}$

2. update 求$P(x_t|y_{1:t})$
依旧使用上面的恒等变形公式:
$P(x_t|y_{1:t}) = P(x_t|y_t,y_{t-1})$
$=$
