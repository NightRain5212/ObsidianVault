
### 方法导论

- *Technique for Order Prederence by Similarity to an Ideal Solution* (TOPSIS)
- 称为优劣解距离法。
- 是一种常用的综合评价方法，能充分利用原始数据的信息，反映各个评价方案的差距。
- 基本概念
	- 理想解：各个属性值都达到各备选方案中最好的值（最优方案）
	- 负理想解：各个属性值都达到各备选方案中最坏的值（最坏方案）
- 排序规则是将各备选方案与理想解与负理想解作比较，最优选择方案是最接近理想解且最远离负理想解的方案。

### 模型原理

- TOPSIS法是一种理想目标相似性的顺序选优技术,在**多目标决策分析**中是一种非常有效的方法。
- 它通过归一化后(去量纲化)的数据规范化矩阵，找出多个目标中最优目标和最劣目标(分别用理归想一解化和反理想解表示),分别计算各评价目标与理想解和反理想解的距离,获得各目标与理想解的贴近度,按理想解贴近度的大小排序,以此作为评价目标优劣的依据。
- 贴近度取值在0~1之间,该值愈接近1,表示相应的评价目标越接近最优水平:反之,该值愈接近0,表示评价目标越接近最劣水平。

### 基本步骤

- 将原始矩阵正向化（使指标均为越大越好的指标）
	- 将原始矩阵正向化，就是要将所有的指标类型统一转化为极大型指标
- 正向矩阵标准化
	- 标准化的方法有很多种，其主要目的就是去除量纲的影响,保证不同评价指标在同一数量级，且数据大小排序不变。
- 计算得分并归一化

### 指标正向化

- 指标类型

|    指标名称    |   指标特点   | 例子  |
| :--------: | :------: | :-: |
| 极大型（效益型）指标 |   越大越好   | 成绩  |
| 极小型（成本型）指标 |   越小越好   | 费用  |
|   中间型指标    | 越接近某个值越好 | 身高  |
|   区间型指标    | 落在某个区间越好 | 体温  |

- 转换公式
	- 极大型：
		- 不需要转化
	- 极小型：
		- $\hat{x} = max - x$ ($\hat{x}$为转换后指标，max为指标最大值)
	- 中间型：
		- $\{x_i\}$ 为一组中间型序列，最优值为$x_{best}$
		- $M=max\{ \left| x_i-x_{best} \right| \}$
		- $\hat{x} =1-\frac{\left|x_i-x_{best}\right|}{M}$
	- 区间型：
		- $M = max\{a-min\{x_i\},max\{x_i\}-b\}$
		- $\widetilde{x}= \begin{cases}1-\frac{a-x_i}{M},\space x_i<a\\1,\space a \le x_i \le b\\1-\frac{x_i-b}{M},\space x_i>b \end{cases}$
	
### 正向化矩阵标准化

- 目的是消除不同量纲的影响
- $x_{ij}$ 表示原矩阵的元素，$z_{ij}$ 表示标准化后矩阵的元素
- $z_{ij} = \frac{x_{ij}}{\sqrt{\sum_{i=1}^{n}{x_{ij}^{2}}}}$

### 指标加权

- 层次分析法，熵权法，Delphi法，对数最小二乘法等

### 计算得分并归一化

- 最大值$Z^{+}=(Z_{1}^{+},Z_{2}^{+},\dots,Z_{m}^{+})$
	- 其中$Z_i^{+}$ 为标准化矩阵第$i$ 列的最大值
- 最小值$Z^{-}=(Z_{1}^{-},Z_{2}^{-},\dots,Z_{m}^{-})$
	- 其中$Z_i^{-}$ 为标准化矩阵第$i$ 列的最小值
- 计算第$i$个对象与最大值的距离
	- $D_i^+ = \sqrt{\sum_{j=1}^{m}{(Z_j^+ - z_{ij})^2}}$
- 计算与最小值的距离
	- $D_i^- = \sqrt{\sum_{j=1}^{m}{(Z_j^- - z_{ij})^2}}$
- 计算得分并归一化
	- $S_i = \frac{D_i^-}{D_i^+ + D_i^-}$
	- $S_i$越大，则$D_i^+$越小，即越接近最大值。
	- $\widetilde{S_i}=\frac{S_i}{\sum_{i=1}^{n}{S_i}}\times 100\%$

### 示例代码
```python
#Topsis算法的示例代码

import numpy as np

# 参评对象数目
# n = int(input("请输入参评对象数目："))
n = 3
# 评价指标数目
# m = int(input("请输入指标数目："))
m = 4

# 接收类型矩阵（表示每一个指标的指标类型）
# kind = input("输入类型矩阵(以空格分开)：1.极大型 2.极小型 3.中间型 4.区间型").split(" ")
kind = [1,2,3,4]
# 接受输入的判断矩阵并转化为np数组
# print("请输入矩阵：")
# A = np.zeros(shape=(n,m))

# for i in range(n):
#     A[i] = input().split(" ")
#     # 将各行元素转化成浮点数
#     A[i] = list(map(float,A[i]))

# print("输入矩阵为：\n{}".format(A))

# 示例矩阵[[9,10,175,120],[8,7,164,80],[6,3,157,90]]
A = np.array([[9,10,175,120],[8,7,164,80],[6,3,157,90]])

# 正向化矩阵
# 正向化指标
# 极小型向极大型转化转化
def mintomax(maxx,x):
    # 先将np数组转化成列表
    x = list(x)
    # x 实际上是列向量
    res = [[(maxx - i)] for i in x]
    return np.array(res)  # 转化为np多维数组

# 中间型转化
def midtomax(bestx,x):
    # 转化为列表
    x = list(x)
    # 找到距离最优点的距离的最大值
    h = [abs(i - bestx) for i in x]
    m = max(h)
    # 防止最大距离值为0的情况
    if m == 0: m = 1
    # 计算正向化的指标 ，将每个距离比去最大距离的比值用1减去
    res = [[(1-i/m)] for i in h]
    # 返回np数组
    return np.array(res)

# 区间型转化
def regtomax(minx,maxx,x):
    # 转化为列表
    x = list(x)
    # 计算超出区间的最大距离
    m = max(minx-min(x),max(x)-maxx)
    res = []
    # 防止最大距离为0
    if m == 0: m = 1 
    # 正向化指标
    for i in x:
        if(i < minx):
            res.append([1-(minx-i)/m])
        elif(i > maxx):
            res.append([1-(i-maxx)/m])
        else:
            res.append([1])
    return np.array(res)

# 正向化矩阵
X = np.zeros((n,m))
for i in range(m):
    if kind[i] == 1:
        v = np.array(A[:,i])
    elif kind[i] == 2:
        v = mintomax(max(A[:,i]),A[:,i])
    elif kind[i] == 3:
        bestA = 165
        v = midtomax(bestA,A[:,i])
    elif kind[i] == 4:
        minx = 90
        maxx = 100
        v = regtomax(minx,maxx,A[:,i])
    if i == 0 :
        # 第一次循环时将得到的正向指标向量转化成列向量
        X = v.reshape(-1,1)
    else:
        # 将剩余的指标列向量垂直合并到一起
        X = np.hstack((X,v.reshape(-1,1)))
print("正向化矩阵为：\n",X)

# 标准化矩阵
for j in range(m):
    X[:,j] = X[:,j]/np.sqrt(sum(X[:,j]**2))

print("标准化矩阵为：\n",X)

# 计算每列(各个指标)的最大值最小值
xmax = np.max(X,axis=0)
xmin = np.min(X,axis=0)
# 求得对象与最优解的距离d+
d_z = np.sqrt(np.sum(np.square((X-np.tile(xmax,(n,1)))),axis=1))
# 与最劣解的距离d-
d_f = np.sqrt(np.sum(np.square((X-np.tile(xmin,(n,1)))),axis=1))

print("每个指标的最大值：",xmax)
print("每个指标的最小值：",xmin)
print("d+向量:",d_z)
print("d-向量:",d_f)

# 计算各指标得分
s = d_f/(d_z+d_f) # s接近于1则较优(随d_z的减小而增大)
s = s/sum(s)      # 归一化处理
s *= 100 # 化为百分制
for i in range(len(s)):
    print(f"第{i+1}个对象的评分为{s[i]}") # 打印评分
```
