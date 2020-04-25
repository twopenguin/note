# 简介与基础

## 版本

Python 主要有两个版本： 2.7.x 和 3.x。两个版本之间存在一些差异，但并不大，它们语法不一样的地方不到 10%。

# 基础语法

## 与其他语言差别

### 缩进是一种语法

Python 不像其他语言一样使用{}或者 begin…end 来分隔代码块，而是采用代码缩进和冒号的方式来区分代码之间的层次关系。所以**代码缩进在 Python 中是一种语法**，如果代码缩进不统一，比如有的是 tab 有的是空格，会怎样呢？会产生错误或者异常。相同层次的代码一定要采用相同层次的缩进。

## 输入

raw_input (Python2.7)

input (python3.x)

```
name = raw_input("What's your name?")
```

## 输出

```python
# 字符串
name = raw_input("What's your name?")
print ('hello,%s' %name)

# 数字
sum = 100+100
print ('sum = %d' %sum)
```

## 判断语句：if … else …

```
if score>= 90:
	print 'Excellent'
else:
	if score < 60:
	   print 'Fail'
    else:
       print 'Good Job'
```

if … else … 是经典的判断语句，需要注意的是在 if expression 后面有个冒号，同样在 else 后面也存在冒号。

## 循环语句：for … in

```python
sum = 0
for number in range(11):
    sum = sum + number
print sum
```

## 循环语句: while

```python
sum = 0
number = 1
while number < 11:
       sum = sum + number
       number = number + 1
print sum
```

## 数据类型

### 列表：[]

```python
lists = ['a','b','c']
lists.append('d')
print lists
print len(lists)
lists.insert(0,'mm')
lists.pop()
print lists
```

列表是 Python 中常用的数据结构，相当于数组，具有增删改查的功能，我们可以使用 len() 函数获得 lists 中元素的个数；使用 append() 在尾部添加元素，使用 insert() 在列表中插入元素，使用 pop() 删除尾部的元素。

### 元组 (tuple)

```python
tuples = ('tupleA','tupleB')
print tuples[0]

# tupleA
```

元组 tuple 和 list 非常类似，但是 tuple 一旦初始化就不能修改。因为不能修改所以没有 append(), insert() 这样的方法，可以像访问数组一样进行访问，比如 tuples[0]，但不能赋值。

### 字典(dictionary)

```python
# -*- coding: utf-8 -*

# 定义一个 dictionary
score = {'guanyu':95,'zhangfei':96}

# 添加一个元素
score['zhaoyun'] = 98
print score

# 删除一个元素
score.pop('zhangfei')

# 查看 key 是否存在
print 'guanyu' in score

# 查看一个 key 对应的值
print score.get('guanyu')
print score.get('yase',99)
```

```python
# 运行结果
{'guanyu': 95, 'zhaoyun': 98, 'zhangfei': 96}
True
95
99
```

字典其实就是{key, value}，多次对同一个 key 放入 value，后面的值会把前面的值冲掉，同样字典也有增删改查。增加字典的元素相当于赋值，比如 score[‘zhaoyun’] = 98，删除一个元素使用 pop，查询使用 get，如果查询的值不存在，我们也可以给一个默认值，比如 score.get(‘yase’,99)。

### 集合：set



# 类库

## NumPy 和 Pandas 库(科学计算工具)

`NumPy` 它不仅是 Python 中使用最多的第三方库，而且还是 SciPy、Pandas 等数据科学的基础库。它所提供的数据结构比 Python 自身的“更高级、更高效”，可以这么说，NumPy 所提供的数据结构是 Python 数据分析的基础。

### 使用 NumPy 让你的 Python 科学计算更高效

为什么要用 NumPy 数组结构而不是 Python 本身的列表 list？这是因为列表 list 的元素在系统内存中是分散存储的，而 NumPy 数组存储在一个**均匀连续的内存块**中。这样数组计算遍历所有的元素，不像列表 list 还需要对内存地址进行查找，从而节省了计算资源。

另外在内存访问模式中，缓存会直接把字节块从 RAM 加载到 CPU 寄存器中。因为数据连续的存储在内存中，NumPy 直接利用现代 CPU 的矢量化指令计算，加载寄存器中的多个连续浮点数。另外 NumPy 中的矩**阵计算可以采用多线程**的方式，充分利用多核 CPU 计算资源，大大提升了计算效率。

当然除了使用 NumPy 外，你还需要一些技巧来提升内存和提高计算资源的利用率。一个重要的规则就是：**避免采用隐式拷贝，而是采用就地操作的方式**。举个例子，如果我想让一个数值 x 是原来的两倍，可以直接写成 x*=2，而不要写成 y=x*2。

这样速度能快到 2 倍甚至更多。

既然 NumPy 这么厉害，你该从哪儿入手学习呢？在 NumPy 里有两个重要的对象：**ndarray**（N-dimensional array object）解决了多维数组问题，而 **ufunc**（universal function object）则是解决对数组进行处理的函数。下面，我就带你一一来看。

### ndarray

ndarray 实际上是多维数组的含义。

在 NumPy 数组中，维数称为秩（rank），一维数组的秩为 1，二维数组的秩为 2，以此类推。在 NumPy 中，每一个线性的数组称为一个轴（axes），其实秩就是描述轴的数量。

#### 创建数组

### ufunc

## Keras 和 TensorFlow(深度学习工具)

## Scikit-learn(机器学习工具)