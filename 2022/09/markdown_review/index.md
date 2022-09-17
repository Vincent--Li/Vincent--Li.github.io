# Markdown复习



## 基础语法

### 标题

Markdown支持6中级别的标题, 对应HTML标签h1-h6

```markdown
# h1
## h2
### h3
#### h4
##### h5
###### h6
```

此外, markdown还支持简单的两级标题设置

```markdown
这是一级标题
===
这是二级标题
---
```

### 段落及区块引用

```markdown
> 这段文字将被高亮显示
```

>这段文字将被高亮显示

### 插入链接或图片

```markdown
[点击跳转至百度](http://www.baidu.com)
![图片](https://upload.jianshu.io/users/upload_avatars/2130822/3507378e03a8.jpeg?imageMogr2/auto-orient/strip|imageView2/1/w/120/h/120)
自动生成连接 <https://www.baidu.com>
```

[点击跳转至百度](http://www.baidu.com)
![图片](https://upload.jianshu.io/users/upload_avatars/2130822/3507378e03a8.jpeg?imageMogr2/auto-orient/strip|imageView2/1/w/120/h/120)
自动生成连接 <https://www.baidu.com>

注: 引用图片和链接的唯一区别就是最前面的叹号

### 列表

Markdown支持有序列表和无序列表两种形式:

- 无序列表使用*或+或-标识
- 有序列表使用数字加.标识,例如1.

```markdown
* 黄瓜
* 玉米
* 茄子

+ 黄瓜
+ 玉米
+ 茄子

- 黄瓜
- 玉米
- 茄子

1. 黄瓜
2. 玉米
3. 茄子
```

以上标记效果如下

- 黄瓜
- 玉米
- 茄子

1. 黄瓜
2. 玉米
3. 茄子

注: 对于有序列表, markdown只关注第一个item的数字编号,然后按次序向后递增. 不管后面的序号是否连续,始终以第一个item的数字编号为锚点

### 使用列表的注意事项

- 如果一个item里面包含了多个段落, *与段落首字母之间需要保留4个空格
- 如果列表钟有区块引用, 区块引用标记符也要缩进4个空格

### 分隔线

有时候为了排版漂亮, 需要加入分隔线. markdown使用下面两种形式之一加入分隔线

```markdown
***
---
```

效果如下, 分隔线语法比较松. 符号之间有空格也可以识别

---

### 强调

有时候, 需要对文字做强调. 使用如下方式

```markdown
*这是斜体*
_这是斜体_
**这是粗体**
__这是粗体__
~~删除~~
```

~~删除~~
*这是斜体*
_这是斜体_
**这是粗体**
__这是粗体__

## 高级用法

### mathjax
参考链接 <https://jingyan.baidu.com/article/4b52d702df537efc5c774bc9.html>

如果使用mathjax需要在FrontMatter中写入`mathjax: true`

#### 公式标记

有两种方式

- 一种是行内标记用\$符号表示

```latex
$(W_1−W_2)x+b_1−b_2=0$
```

这是一个行内公式 $(W_1−W_2)x+b_1−b_2=0$

- 一种是整行公式用两个\$\$

```latex
$$
(W_1−W_2)x+b_1−b_2=0
$$
```

效果如下
$$
(W_1−W_2)x+b_1−b_2=0
$$

#### 特殊符号

名称| 大写| 写法| 小写| 写法
--| -- | -- | -- | --
alpha |A| A |α| \alpha
beta| B| B| β |\beat
gamma| Γ |\Gamma| γ| \gamma
delta| Δ| \Delta| δ |\delta
epsilon |E| E| ϵ| \epsilon
zeta| Z| Z| ζ |\zeta
eta |H| H |η| \eta
theta| Θ |\Theta |θ| \theta
iota| I |I| ι |\iota
kappa| K| K| κ| \kappa
lambda| Λ| \Lambda| λ| \lambda
mu| M| M| μ| \mu
nu| N| N |ν| \nu
xi| Ξ| \Xi| ξ| \xi
omicron| O| O| ο |\omicron
pi| Π| \Pi| π| \pi
rho |P| P| ρ| \rho
sigma| Σ| \Sigma| σ| \sigma
tau |T| T| τ| \tau
upsilon| Υ| \Upsilon| υ| \upsilon
phi |Φ| \Phi| ϕ| \phi
chi| X| X| χ| \chi
psi| Ψ| \Psi| ψ| \psi
omega| Ω| \Omega| ω| \omega

#### 上标/下标

上标和下标分别使用^和_来实现

```latex
$x_i^2=10$
$log_2^x=16$
```

效果如下
$x_i^2=10$
$log_2^x=16$

上下标默认只读后面一个字符, 如果有多个字符需要用{}包裹

```latex
$10^{10}$
$10^10$
```

效果如:
$10^{10}$
$10^10$

如果左右两边都要有上下标

```latex
${^1_2}A{^3_4}$
```

效果如${^1_2}A{^3_4}$

#### 括号

- 小括号与方括号: 使用原始的小括号和方括号即可 $(2+3)[4+4]$
- 大括号: 需要使用转义符号\\\{和\\\} 如: \$\\\{a*b\\\}\$ = $\{a*b\}$
- 尖括号: 使用\langle和\rangle分别表示左尖括号和右尖括号\$\langle x \rangle\$  == $\langle x \rangle$
- 上取整: 使用\lceil和\rceil表示. 如: \$\lceil x \rceil\$ == $\lceil x \rceil$
- 下取整: 使用\lfloor和\rfloor表示. 如 \$\lfloor x \rfloor\$ = $\lfloor x \rfloor$
- 需要注意原始括号不会根据公式的大小缩放. 可以使用\left(...\right)来缩放. 如: $\left( \frac1{\frac12} \right)$ 和 $(\frac1{\frac12})$

#### 求和/积分/多重积分

- \sum表示求和, 下标表示求和下限, 上标表示求和上限

```latex
$\sum_1^n$
```

效果如: $\sum_1^n$

- \int表示求积分, 下标表示求和下限, 上标表示求和上限

```latex
$\int_1^\infty$
```

效果如: $\int_1^\infty$

- \iint表示求积分, 下标表示求和下限, 上标表示求和上限

```latex
$\iint_1^\infty$
```

效果如: $\iint_1^\infty$

- 类似标签

```latex
$\prod_1^n$ == ∏

$\bigcup$ == ⋃

$\bigcap$ == ⋂

$\iint$ == ∬

$\approx$ == ≈

$\times$ == ×

$\partial$ == ∂

$\vec{a}$

$1 \choose n$

$\hat{y}$

$\overline{y}$

$\underline{abc}$

$$
\begin{bmatrix}
1 & 2 & 3 \\
4 & 5 & 6 \\
\vdots & \vdots & \vdots \\
\end{bmatrix}
$$

$$
f(x) = 
\begin{cases}
x + 1 & x \gt 1 \\
2x & others
\end{cases}
$$
```

效果如:
$\prod_1^n$

$\bigcup$

$\bigcap$

$\iint$

$\approx$

$\times$

$\partial$

$\vec{a}$

$1 \choose n$

$\hat{y}$

$\overline{y}$

$\underline{abc}$

$$
\begin{bmatrix}
1 & 2 & 3 \\
4 & 5 & 6 \\
\vdots & \vdots & \vdots \\
\end{bmatrix}
$$

$$
f(x) = 
\begin{cases}
x + 1 & x \gt 1 \\
2x & others
\end{cases}
$$

#### 分式/根式

分式两种方式:

- 使用\$\frac ab\$, 结果为$\frac ab$. 如果分子分母不是单个字符需要用{}
- 使用\over来分隔, \${a+1\over b+1}\$ == ${a+1\over b+1}$

根式用\\\$\sqrt[a]b\\\$表示:
其中，方括号内的值用来表示开几次方，省略方括号则表示开方, 如:
例一: \\\$\sqrt[4]{\frac xy}\\\$ == $\sqrt[4]{\frac xy}$
例二: \\\$\sqrt{x^3}\\\$ == $\sqrt{x^3}$

### 插入代码块

- 行内代码, 单组反引号
- 代码块, 两个以上反引号包裹
这是一段`var x=3`行内代码
以下是代码块

```javascript
fun(x: Int, y:Int): Int {
    return x + y
}
```

### 插入表格

```markdown
表头|条目一|条目二
:---:|:---:|:---:
项目|项目一|项目二
```

以上标记显示效果如下:

表头|条目一|条目二
:---:|:---:|:---:
项目|项目一|项目二

### 流程图

详见 <https://mermaidjs.github.io/>

```text
...mermaid ...请替换成反引号
graph TD
client1-->|read / write|SVN((SVN server))
client2-->|read only|SVN
client3-->|read / write|SVN
client4-->|read only|SVN
client5(...)-->SVN
SVN---|store the data|sharedrive
...
```

效果如下

{{<mermaid>}}
graph TD;
  client1-->|read / write|SVN((SVN server));
  client2-->|read only|SVN;
  client3-->|read / write|SVN;
  client4-->|read only|SVN;
  client5(...)-->SVN;
  SVN---|store the data|sharedrive;
{{</mermaid>}}

### 甘特图

```text
...mermaid 将...替换成反引号
gantt
        dateFormat  YYYY-MM-DD
        title Adding GANTT diagram functionality to mermaid
        section A section
        Completed task            :done,    des1, 2014-01-06,2014-01-08
        Active task               :active,  des2, 2014-01-09, 3d
        Future task               :         des3, after des2, 5d
        Future task2               :         des4, after des3, 5d
        section Critical tasks
        Completed task in the critical line :crit, done, 2014-01-06,24h
        Implement parser and jison          :crit, done, after des1, 2d
        Create tests for parser             :crit, active, 3d
        Future task in critical line        :crit, 5d
        Create tests for renderer           :2d
        Add to mermaid                      :1d
...
```

效果如下

{{<mermaid>}}
gantt
        dateFormat  YYYY-MM-DD
        title Adding GANTT diagram functionality to mermaid
        section A section
        Completed task            :done,    des1, 2014-01-06,2014-01-08
        Active task               :active,  des2, 2014-01-09, 3d
        Future task               :         des3, after des2, 5d
        Future task2               :         des4, after des3, 5d
        section Critical tasks
        Completed task in the critical line :crit, done, 2014-01-06,24h
        Implement parser and jison          :crit, done, after des1, 2d
        Create tests for parser             :crit, active, 3d
        Future task in critical line        :crit, 5d
        Create tests for renderer           :2d
        Add to mermaid                      :1d
{{</mermaid>}}


### UML序列图

语法参考：<https://bramp.github.io/js-sequence-diagrams/>

```text
sequenceDiagram
    participant Alice
    participant Bob
    Alice->John: Hello John, how are you?
    loop Healthcheck
        John->John: Fight against hypochondria
    end
    Note right of John: Rational thoughts <br/>prevail...
    John-->Alice: Great!
    John->Bob: How about you?
    Bob-->John: Jolly good!
```

效果如下

{{<mermaid>}}
sequenceDiagram
    participant Alice
    participant Bob
    Alice->John: Hello John, how are you?
    loop Healthcheck
        John->John: Fight against hypochondria
    end
    Note right of John: Rational thoughts <br/>prevail...
    John-->Alice: Great!
    John->Bob: How about you?
    Bob-->John: Jolly good!
{{</mermaid>}}

## 其它

### 特殊符号处理

\\反斜杠插入用到的特殊符号,主要用到以下几种特殊符号

```markdown
\   反斜线
`   反引号
*   星号
_   底线
{}  花括号
[]  方括号
()  括弧
#   井字号
+   加号
-   减号
.   英文句点
!   惊叹号
```

### 给文字上色

使用html方式给文字上色

```markdown
<font color='#ff0000'>测试红色</font>
```

效果如下
<font color='#ff0000'>测试红色</font>

