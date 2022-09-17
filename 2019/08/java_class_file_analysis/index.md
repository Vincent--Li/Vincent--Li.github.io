# Java Class文件结构


### 语言无关性

{{<mermaid>}}
graph LR
  A(*.java)-->D(.class)
  B(*.rb)-->D
  C(*.groovy)-->D
  E(其它JVM语言)-->D
  D-->F(JVM)
{{</mermaid>}}

### 文件结构
u1 = 1byte

类型|名称|数量
-|-|-
u4|magic|1
u2|minor_version|1
u2|major_version|1
u2|constant_pool_count|1
cp_info|constant_pool|constant_pool_count-1
u2|access_flags|1
u2|this_class|1
u2|super_class|1
u2|interfaces_count|1
u2|interfaces|interfaces_count
u2|fields_count|1
field_info|fields|fields_count
u2|methods_count|1
method_info|methods|methods_count
u2|attribute_count|1
attribute_info|attributes|attributes_count

#### magic u4
- 0xCAFEEBABE

#### constant_pool_count u2
常量池中, 常量的个数

#### constant_pool cp_info
- CONSTANT_Utf8 1 UTF-8编码的Unicode字符串
- CONSTANT_Integer 3 int类型的字面值
- CONSTANT_Float 4 float类型的字面值
- CONSTANT_Long 5 long类型的字面值
- CONSTANT_Double 6 double类型的字面值
- CONSTANT_Class 7 对一个类或接口的符号引用
- CONSTANT_String 8 String类型字面值的引用
- CONSTANT_Fieldref 9 对一个字段的符号引用
- CONSTANT_Methodref 10 对一个类中方法的符号引用
- CONSTANT_InterfaceMethodref 11 对一个接口中方法的符号引用
- CONSTANT_NameAndType 12 对一个字段或方法的部分符号引用

#### access flag u2: 类的标示符
flag name|value|interpretation
-|-|-
ACC_PUBLIC|0x0001|public
ACC_FINAL|0x0010|final,不能被继承
ACC_SUPER|0x0020|是否允许使用invokespecial指令,jdk1.2后,该值为true
ACC_INTERFACE|0x0200|是否是接口
ACC_ABSTRACT|0x0400|抽象类
ACC_SYNTHETIC|0x1000|该类不是由用户代码生成,运行时生成的,没有源码
ACC_ANNOTATION|0x2000|是否为注解
ACC_ENUM|0x4000|是否为枚举

#### this_class u2
- 指向常量池的Class

#### super_class u2
- 指向常量池的Class

#### interface_count u2
- 接口数量

#### interfaces
- interfaces_count 个interface u2
- 每个interface是指向CONSTANT_Class的索引

#### field_count
- 字段数量

#### fields
- field_count个field_info

#### field
- access_flags u2
- name_index u2
- descriptor_index u2
- attributes_count u2
- attribute_info attributes[attributes_count];



