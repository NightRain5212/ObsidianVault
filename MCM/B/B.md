## 问题重述：

- 在阿拉斯加朱诺建立可持续旅游业模型。您可能需要考虑游客数量、总收入以及为稳定旅游业而采取的措施等因素。清楚地说明您正在优化哪些因素，以及哪些因素作为限制。包括任何额外收入的支出计划，并说明这些支出如何反馈到您的模型中以促进可持续旅游业。包括敏感性分析并讨论哪些因素最重要。
- 展示您的模型如何适应另一个受过度旅游影响的旅游目的地。地点的选择如何影响最重要的措施？您如何使用您的模型来宣传游客较少的景点和/或地点，以实现更好的平衡？
- 向朱诺旅游委员会写一份一页备忘录，概述您的预测、各种措施的效果以及如何优化结果的建议。

# 问题1

- 建立多目标优化模型
- 目标函数Z :N(游客数)，E(经济效益函数)，C(环境代价函数)，S（社会满意度）,$t$代表税率
$$maximize  \space \space Z = max\space E(N,t)+min\space C(N) +max\space S(N)$$
$$s.t.\space \space 0\le N \le N_{lim}，C(N)\le C_{lim}，S_{lim} \le S(N)$$

- $C(N)$环境代价函数：$I$为环境代价人均影响系数，随游客数增加，碳排放量与冰川消融的速率都与游客数正相关,$I$为相关系数。
$$C = IN$$
- $I$的计算：已知2010-2020年每年年均冰川流失量$I_y$，和每年游客量$a_i$，计算每年人均影响的冰川流失系数为
$$I=\frac{I_y}{\frac{1}{10}\sum a_i}$$
	- 数据来源： https://www.xueshushe.cn/article/76215c266707fab53cdefabe714e151a

- 收益函数$E(N)$：建立一个关于游客人数和旅游业收益的函数。这个函数考虑到游客人数对收益的正向影响（如更多的游客带来更多的收入）以及可能的负向影响（如过度拥挤导致的游客体验下降）
$$E = (aN^2+bN+c)(1+t)$$
- 社会满意度$S(N)$：$J$为社会满意度人均影响系数，d为初值
$$S = JN+d $$

	- 数据来源: https://www.jedc.org/wp-content/uploads/2024/11/2024-JEDC-Juneau-SEAK-Economic-Indicators-and-Outlook-Report.pdf


- 数据处理：舍去了疫情期间的不合理数据。

- 边界值的估计
	- 游客数大于0小于游客限制数$N_{lim}=1200000$
	- 环境代价不超过可接受的最大值$C_{lim}=5160$
	- 社会满意度不低于可接受的最小值$S_{lim}=0.3762$

- 限制因素：
	- 游客数大于0小于游客限制数$N_{lim}$，$0\le N \le N_{lim}$
	- 环境代价不超过可接受的最大值$C_{lim}$，$C(N)\le C_{lim}$
	- 社会满意度不低于可接受的最小值$S_{lim}$，$S_{lim} \le S(N)$

- 我们使用最小二乘法进行线性回归拟合，
- 最小二乘法的目标是最小化残差平方和（RSS）：

$$\mathrm{RSS}=\sum_{i=1}^n(y_i-\hat{y}_i)^2$$
- 其中：

$$\hat{y}_i=\beta_0+\beta_1x_i$$
- 求解回归系数：
- 对 RSS 关于 β0​ 和 β1 求导，并令导数为零，得到正规方程：
$$\frac{\partial\mathrm{RSS}}{\partial\beta_0}=-2\sum_{i=1}^n(y_i-\beta_0-\beta_1x_i)=0$$
$$\frac{\partial\mathrm{RSS}}{\partial\beta_1}=-2\sum_{i=1}^n(y_i-\beta_0-\beta_1x_i)x_i=0$$
- 解正规方程，得到回归系数：

$$\beta_1=\frac{\sum_{i=1}^n(x_i-\bar{x})(y_i-\bar{y})}{\sum_{i=1}^n(x_i-\bar{x})^2}$$
$$\\\beta_0=\bar{y}-\beta_1\bar{x}$$

- 使用最小二乘法进行二次拟合：

	- 构造$X=[1,N,N^2]$
	- 构造系数向量$\beta = [c,b,a]$
	- 构建方程$Y=X\beta+\epsilon$，$\epsilon$为误差向量
	- 残差平方和$RSS=\sum_{i=1}^n (y_i-\hat y_i^2)$
	- 其中$\hat y_i = aN^2+bN+c$
	- 求解方程$X^TX\beta=X^TY$，可得系数向量$\beta=(X^TX)^{-1}X^TY$

- 计算结果：(计算过程具体化：线性回归与非线性回归的过程)
	- $J = -9.58e-08, d = 4.72e-01$
	- $I=0.00516$  $km^3/(person · year$)
	- $a = 5.73e-06, b = 2.59e+01, c = 7.52e+07$

 - 设置权重,$w_i$代表权重
$$ maximize  \space \space Z = \omega_1 E(N,t)-\omega_2\space C(N) +\omega_3\space S(N)$$
$$\sum_{i=1}^{3} \omega_i =1，\omega_1=0.4，\omega_2=0.3，\omega_3=0.3$$

- 非线性规划得出最优解

	- 最终目标函数,以及约束条件（$t$初值为0.05）
$$Z(N,t)=0.4\frac{1}{E_{max}}(aN^2+bN+c)(1+t)-0.3\frac{1}{C_{max}}IN+0.3\frac{1}{S_{max}}(JN+d)$$
$$s.t.\space \space 0\le N \le N_{lim}，C(N)\le C_{lim}，S_{lim} \le S(N)$$

-  通过拉格朗日函数将约束优化问题转化为无约束优化问题,
	- $\mathcal{L}(N,\lambda)=Z(N)+\sum_i\lambda_i\cdot g_i(N)$,
	- 在当前迭代点 xk​，SLSQP 对拉格朗日函数进行二次近似，构建二次规划（QP）子问题,$\min_{\Delta x}\frac12\Delta x^TH_k\Delta x+\nabla f(x_k)^T\Delta x$
	- $H_k$ 是拉格朗日函数的 Hessian 矩阵 (或近似 Hessian 矩阵),$\nabla f(x_k)$是目标函数在 $x_k$处的梯度。
	- 使用二次规划算法求解子问题，得到搜索方向 $Δx$。
	- 在搜索方向 $Δx$上进行线搜索，确定步长 $α$，使得目标函数在 $x_{k+1}=x_k+αΔx$ 处有显著改进。
	- 更新迭代点：$x_{k+1}=x_k+αΔx$
	- 检查当前解是否满足收敛条件：
		- 如果满足收敛条件，则停止迭代；否则，返回步骤 3。

	- 变量初始值为均值$\hat x = \frac{1}{n}\sum_{i=1}^{n}a_i$
	- 最优解： 998990.19987076
	- 目标函数的最大值： 0.31244643157150676

- 敏感度分析：
	- 我们生成Sobol序列样本，不同的$X_i$与对应的$Y$，并用皮尔逊相关系数（Pearson Correlation Coefficient）计算每个分函数对目标函数的敏感性
	- $r=\frac{\sum(X_i-\bar{X})(Y_i-\bar{Y})}{\sqrt{\sum(X_i-\bar{X})^2\sum(Y_i-\bar{Y})^2}}$
	- $X_i \in \{Z_{E}=E(x),Z_{C}=C(x),Z_{S}=S(x)\}$，$Y = Z(E,C,S)$
	- E(N, t) : 0.98000000
	- C(N) : 0.56018732
	- S(N) : 0.56018732
	- 得出结论：经济因素的影响最大，环境与社会满意度因素为次要因素。

# 问题2

- 展示您的模型如何适应另一个受过度旅游影响的旅游目的地。地点的选择如何影响最重要的措施？您如何使用您的模型来宣传游客较少的景点和/或地点，以实现更好的平衡？

- 熵权法确定各分函数的权重
	- 我们获取了近年来的数据来确定指标：
	- $e_i$，旅游收益
	- $c_i$，碳排放
	- $s_i$，社会满意度
- 并进行了去量纲处理，和归一化处理，
	- 处理后的值为$Y_i$，处理前的值为$X_i$，$X_i \in \{e_i,c_i,s_i\}$
	- 对于经济和满意度方面进行正向归一化：
$$Y_i = \frac{X_i-min\{X_i\}}{max\{X_i\}-min\{X_i\}}$$
	- 对于环境代价进行负向归一化：
$$Y_i = \frac{min\{X_i\}-X_i}{max\{X_i\}-min\{X_i\}}$$
	- 计算指标比重：
$$p_i=\frac{Y_i}{\sum_{i=1}^nY_i}$$
	- 计算熵值：
$$e_i=-\frac{1}{ln(n)}\sum_{i=1}^np_iln(p_i)$$
	- 计算差异系数：
$$d_i=1-e_i$$
	- 计算权重：
$$\omega_i=\frac{d_i}{\sum_{i=1}^{n}d_i}$$
- 计算结果：
	- 巴塞罗那：$\omega_1=0.2，\omega_2=0.3，\omega_3=0.5$
	- 威尼斯：$\omega_1=0.15，\omega_2=0.5，\omega_3=0.35$
	- 希腊雅典：$\omega_1=0.38，\omega_2=0.25，\omega_3=0.37$

