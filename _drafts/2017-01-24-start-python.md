---
layout: post
title: Python学习笔记：基础入门
category: 学习笔记
tags:
  - Python
  - 学习笔记
date: 2017-01-24 11:40:29
updated: 2017-02-01 21:46:04
---

## 基本语法

### Hello, World

第一个当然是从hello world开始。Python的hello world非常简单，只有一行：

```python
print('Hello, World!')
```

Python是解释型语言，不需要主函数或主类作为程序入口，而是读到哪执行到哪（当然，定义的表达式、函数、类不会被立即执行，只会暂存，调用时再执行），对于hello world，自然就只需要用打印函数直接打印就行。

`print()` 是python的内建函数(build-in function)，它接受任意个对象作为参数，且可以设置分割符、结束符等参数，详见 [*print*](https://docs.python.org/3/library/functions.html?highlight=print#print)。

与之对应的是获取输入的`input()`函数，它接受一个参数作为输入提示信息，返回用户输入的一行字符（字符串类型），详见[*input*](https://docs.python.org/3/library/functions.html#input)。

### 基本数据类型

Python的基本数据类型包括：整型、浮点型、字符串、布尔型。

#### 整型

python中整型数没有范围限制，可以任意大（受内存限制= =）。

```python
>>> 1234**123
170515806212727042875059727620626282654302313111068290470529619322183913834868007471366306717060598572641592314554345900570589670671499709086102539904846514793135617305563669993950104622035682027355757755070083238444147778396026387067042685700404003287042480639680696865587865016699383883388831980459159942845372414601809429717726107628595243406801014418529766279838067203562799104
```

对于非整型对象可以使用`int()`函数进行强制转换。

```python
>>> int('010')
10
>>> int(12.34)
12
>>> int(12.99)
12
```

`int()`也是一个内建函数，还有一个`base`参数用以指明按照多少进制转换，默认为10，详见[*int*](https://docs.python.org/3/library/functions.html?highlight=print#int)

```python
>>> int('010', base=2)
2
>>> int('aa', base=16)
170
```

#### 浮点型

不同于整型，浮点数有大小限制，定义时超出范围会自动截断，计算时超出范围则会抛出`OverflowError`异常。浮点数可以用带小数点的数表示，也可以用科学计数法表示。

```python
>>> .123
0.123
>>> 1e-6
1e-06
>>> 1E90
1e+90
>>> 1.234567890987654321234567890
1.2345678909876543
>>> 12.34**12
12467572902176.588
>>> 12.34**1234
Traceback (most recent call last):
  File "<pyshell#54>", line 1, in <module>
    12.34**1234
OverflowError: (34, 'Result too large')
```

浮点数的范围在`sys`模块有定义，超出这个范围的值为无限大`inf`。非浮点数类型也可以使用`float()`函数进行强制转换，详见[*float*](https://docs.python.org/3/library/functions.html#float)

```python
>>> import sys
>>> sys.float_info
sys.float_info(max=1.7976931348623157e+308, max_exp=1024, max_10_exp=308, min=2.2250738585072014e-308, min_exp=-1021, min_10_exp=-307, dig=15, mant_dig=53, epsilon=2.220446049250313e-16, radix=2, rounds=1)
>>> float('+1.23')
1.23
>>> float('   -12345\n')
-12345.0
>>> float('1e-003')
0.001
>>> float('+1E6')
1000000.0
>>> float('-Infinity')
-inf
```

#### 字符串

用单引号`'`或双引号`"`括起来的文本表示字符串。（特殊的，使用三个引号`"""`或`'''`括起来的可用于表示多行字符串，常见于文档注释。）字符串中包含特殊字符时使用反斜杠`\`来转义。（如若字符串中包含的是引号则可以用另一种类型的引号来表示字符串而不需要用转义字符。）在字符串前加一个`r`或`R`表示原始字符串（raw strings），即可以去掉转义，常见于正则表达式。

```python
>>> 'abc123'    # 单引号字符串
'abc123'
>>> "abc 123"   # 双引号字符串
'abc 123'
>>> """abc
123
   opq456"""    # 三重引号多行字符串
'abc\n123\n   opq456'
>>> print('abc\t\n\'123')  # 字符转义
abc	
'123
>>> print("abc\t\n'123")   # 使用双引号避免了对单引号的转义，跟上面的打印截过相同
abc	
'123
>>> print(r'c:\windows\tmp')    # 加上r使得反斜杠不许转义即可打印
c:\windows\tmp
>>> print('c:\windows\tmp')     # 未加则将\t识别为tab字符（其中\w因为不是特殊字符所以识别为两个字符）
c:\windows	mp
```

python中的字符串可以进行加法（拼接）或乘法（多次拼接）运算。

```python
>>> 'hello ' + 'python ' + '!'
'hello python !'
>>> 'hello ' * 3
'hello hello hello '
```

内建函数`str()`可以将其它类型对象强制转换为字符串型。（实际上为调用对象的`__str__()`方法，详见面向对象编程部分。）

```python
>>> str(123.456)
'123.456'
>>> class A:
	def __str__(self):
		return 'A class'
>>> str(A)
"<class '__main__.A'>"    # 默认返回值
>>> str(A())
'A class'                 # 实例化后调用对象的__str__()方法的返回值
```

#### 布尔型

布尔值只有`True`和`False`两种类型的值。要么是`True`，要么是`False`。布尔型可以用`and`、`or`、`not`进行与或非逻辑运算。常用在条件判断中。（0和空的数据类型如空列表、空元祖、空集合及`None`等都返回`False`，非零和非空数据类型返回`True`。实际上对象的真值由其`__bool__()`方法决定，详见面向对象编程部分。）

```python
>>> a = set()
>>> if not a:print('a')
a
>>> if not []: print('null')
null
>>> 0 == False
True
>>> 3 == True
False
>>> 1 == True
True
>>> class A:
	def __init__(self, a):
		self.a = a
	def __bool__(self):
		if self.a > 5:
			return True
		else:
			return False
>>> if A(10):print('true')
true      # 用10实例化返回真
>>> if A(5):print('true')
          # 用5实例化返回假
```

#### 空类型

空值`None`是一种特殊的数据类型，跟其它面向对象语言中的`null`的作用类似。

#### 变量定义

在定义变量时，不需要指明变量类型。Python作为一门动态语言，变量的数据类型是可以动态改变的。定义变量时只需给定变量名并赋值即可，python解释器会根据赋的值的类型来推断该数据的类型。变量名必须是大小写英文、数字和 _ 的组合，且不能用数字开头。

```python
>>> a = '123'   # 将a赋字符串型值
>>> type(a)
<class 'str'>
>>> a = 123     # 重新将a赋整型值，则a变为整型
>>> type(a)
<class 'int'>
```

需要注意的是，赋值的过程实际上是建立起名字对内存值的引用。

```python
>>> a = 123  # 创建123对象并将a指向该对象
>>> b = a    # 将b指向a所指对象而非重新创建
>>> a
123
>>> b
123
>>> a = 456  # 创建456对象并将a指向该对象
>>> a
456
>>> b        # b所指仍为123对象
123
```

### 循环

循环用`while`来实现，且支持`continue`关键字来跳过当前循环和`break`关键字来强制结束循环，格式如下：

```python
while <expression>:
    # do something here
    pass
```

Python是使用冒号和缩进来控制作用域的，因此对代码格式要求比较严格，一旦缩进错误，整个逻辑都可能错误。另外，这还涉及到tab、2 space、4space的争议问题，当然这根据语言的不同有不同，[这里](https://ukupat.github.io/tabs-or-spaces/)有一个统计。在python中，还是使用空格更方便。此外在编辑器或IDE中将translate_tab_to_space选项打开以避免混用tab和space的错误。

### 条件

条件的三个关键字为 `if`、`elif` 和 `else` ，格式如下：

```python
if <expression1>:
    # do something here
elif <expression2>:
    # do others
else:
    # e....
```

python中使用`if`和`else`来实现三元操作符：

```python
>>> x,y = 4,5
>>> small = x if x < y else y
>>> small
4
```

### 操作符

