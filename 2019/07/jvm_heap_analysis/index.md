# Java堆分析



### MAT使用基础 Memory Analyse Tool
#### 浅堆(Shallow Heap) 
- 一个对象结构所占用的内存大小
- 3个int类型以及一个引用类型合计占用3*4+4=16字节, 再加上对象头的8个字节,因此String对象占用的控件,即浅堆的大小是16+8=24字节
- 对象大小按照8字节对齐
- 浅堆大小和对象的内容无关, 只和对象的结构有关

#### 深堆(Retained Heap)
- 一个对象被GC后,可以真实释放的内存大小
- 只能通过对象访问到的(直接或者间接)所有对象和浅堆之和(支配树)

#### 显示入引用(incoming)和出引用(outgoing)


#### 支配树
- 对象引用图中, 所有指向对象B的路径都经过对象A, 则认为对象A支配对象B
- 如果对象A是离对象B最近的一个支配对象,则认为对象A为对象B的直接支配者
- 支配者被回收, 被支配者也被回收

#### 对象引用图
{{<mermaid>}}
graph TD
  A((A))-->C((C))
  B((B))-->C
  C-->D((D))
  C-->E((E))
  E-->G((G))
  G-->H((H))
  D-->F((F))
  F-->D
  F-->H
{{</mermaid>}}

#### 支配树
{{<mermaid>}}
graph TD
  root((root))-->A((A))
  root-->B((B))
  root-->C((C))
  C-->D((D))
  C-->E((E))
  C-->H((H))
  D-->F((F))
  E-->G((G))
{{</mermaid>}}



