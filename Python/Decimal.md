
# Py高精度浮点数的实现

- 转化为整数运算（理论上把浮点数转换成整数运算即可，python的整数是没有精度限制的）

> [!NOTE] Tip
> 可以跟C/C++风格的整数区分开（有长度限制）。

- `Decimal` 模块
# Decimal模块的引入

- 由于`python`的浮点数**精度是有限的**(17位)，为了更精确的计算浮点数，于是引入了`Decimal` 模块。
- 一个 `Decimal` 对象能够**精确地表示任何数字**，以及能够无条件进位或无条件舍去到任意的精度。
```
import deciaml
```


# Decimal模块的使用

- 首先要先确保导入了本模块，本模块为内置模块，无需pip安装
## 创建Decimal类型变量

### 变量构造

- 可以基于整数、字符串、浮点数或元组构造 Decimal 实例。
	- 不要用float啦，都要高精度运算了还用低精度的数据。
```
import decimal

a = decimal.Decimal(1)
b = decimal.Decimal('3.14')
c = decimal.Decimal(3.14) # 注意精度问题
d = decimal.Decimal((0, (3, 1, 4), -2)) #符号位(1为负数,0为正数)，构造元组，指数位

# 下面的代码更简洁一点，导入decimal全部模块，可以不用写前面的decimal.
from decimal import *
a = Decimal(1)
b = Decimal('3.14')
c = Decimal(3.14) # 注意精度问题
d = Decimal((0, (3, 1, 4), -2)) #符号位(1为负数,0为正数)，构造元组，指数位

```

## 上下文

- 查看当前上下文(包括精度，舍入，指数限制，等等)
```
from decimal import *
getcontext()
```

## 设置小数精度

### quantize使用

- `quantize() `函数将数字四舍五入格式化
- 该函数接受参数为：作为格式化模板的decimal对象，与舍入方法
```
from decimal import *

fm1 = decimal.Decimal("0.01")
# 保留两位小数，且截断舍入(舍去所有精确位数之后的数)
>>> decimal.Decimal("2.718").quantize(fm1,rounding=ROUND_DOWN)
Decimal('2.71')

# 保留y一位小数，且进一舍入
fm2 = decimal.Decimal("0.1")
>>> decimal.Decimal("2.718").quantize(fm2,rounding=ROUND_UP)
Decimal('2.8')
```
### 全局精度

- 通过`decimal.getcontext().prec`设置全局精度。
- 可以设置有效数字可以决定小数点后有效位数，在截取有效位数时，默认遵守**四舍五入**原则
```
import decimal

decimal.getcontext().prec = 4
print(decimal.Decimal(1)/decimal.Decimal(6)) #0.1667
print(1/6) #0.16666666666666666
```

### 局部精度

- 使用`decimal.localcontext()`设置局部精度，不会影响全局设置。
```
# 设置局部精度为8
with localcontext() as ctx:
    ctx.prec = 8
    a = decimal.Decimal("3.1415926535")
    a *= 2

>>> a
Decimal('6.2831853')
>>> getcontext().prec
4 # 不影响全局精度
```
# 代码示例——精确e的后200位

```
# 利用泰勒公式近似e
from decimal import *
# e的初值f(0)
e = Decimal("1")
# 循环次数
k = 1000
# 循环计数器
i = 1
# 保存当前的阶乘结果
c = Decimal("1")

# 考虑到尾部浮动误差，精度适当取大
getcontext().prec = 202
# 泰勒展开！
for i in range(1,k+1):
    c = c / i
    e += c
    i += 1
# 输出
print(e)
```
