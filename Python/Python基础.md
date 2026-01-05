
## python 特点

- 解释性语言（不产生目标程序，解释程序逐句翻译，计算机逐句执行）
- 面向对象
- 夸平台
- 模块编程

## python环境

- python解释器
- Jupyter Notebook：代码编辑器
- Conda：包管理器

## 变量

> [!NOTE] PYTHON是动态类型语言
> 1.变量名是一个容器，**储存真实值的地址**，真实值储存在另一块空间中。
> 2.创建变量**不需要提前声明变量**。
> 3.变量名只是对象的**引用**。

![[python变量示例.png]]


### 变量赋值的过程

```
a = 2           #1
a = a + 3       #2
```
- 先在内存中创建一个空间，储存数值为2的整数。
- 再在内存中创建一个名字为a的变量，储存保存2的空间的地址（指向2的空间）
- 在#2语句，则在内存创建了另一块空间，保存a+3的值，再将a指向新数值
- py自动删除没有指向的值（垃圾回收机制）

## [[运算符]]

> [!warning] TIPS
> 1.运算符`/`的运算结果一定是**浮点数**

## 整数与浮点数的区别



> [!warning] WARNING
> **整数型**的精准度是**无限**的。
> 而Python中的**浮点数**最多能精准到连小数点的前与后在内**大约18位**。

![[1.png]]

![[2.png]]


# 与C的区别

- 变量类型可以变化
- py的整数大小是无限的
- py的list可以存放不同的类型的元素，长度可以变化
- 可以实现变量交换`a,b = b,a`
- 

## 数据类型

- 布尔类型
- 数值类型，
	- 整数
	- 浮点数
- 序列
	- 列表
	- 字符串
	- 元组
- 字典


> [!warning] Warning
> for循环语句中的range迭代的i，不会被循环体中的语句改变

```
#e.g.1
>>> for i in range(5):
       i+=2
       print(i,end=" ")
   
2 3 4 5 6
# 而不是2，4，6

# e.g.2
>>> for i in range(len(l)):
    l.append(5)
    print(l)
    
[0, 1, 2, 3, 4, 5]
[0, 1, 2, 3, 4, 5, 5]
[0, 1, 2, 3, 4, 5, 5, 5]
[0, 1, 2, 3, 4, 5, 5, 5, 5]
[0, 1, 2, 3, 4, 5, 5, 5, 5, 5]
#而不会无限循环

>>> for i in l:
    	l.append(5)
    	print(l)
#此时会无限循环
```


> [!tip] Tip
> 要注意循环语句中的i不要随意改动，否则会无限循环

# string 与 list

- string不能改动而list可以改动


# 函数的局部变量与全局变量

```python
a = 10  # a为全局变量
def f(b):
    print(a+2) 
    a = b+2  # a为局部变量？与a的全局属性冲突，则不会运行
    return;

>>> f(0)
Traceback (most recent call last):
  File "<pyshell#25>", line 1, in <module>
    f(0)
  File "<pyshell#24>", line 2, in f
    print(a+2)
UnboundLocalError: cannot access local variable 'a' where it is not associated with a value
```

- python 会先检索函数体内部的所有的局部变量，再执行函数，如果局部变量与全局变量冲突则无法运行。