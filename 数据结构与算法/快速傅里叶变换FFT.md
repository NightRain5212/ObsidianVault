
## 前置知识

### 多项式的表示

- 任何一个$n$次多项式可以写成如下形式
$$f_n(x) = a_0+a_1x+a_2x^2+...+a_nx^n=\sum_{i=0}^{n}a_ix^i$$

- **系数形式**：当我们确定$n$次多项式的$n$个系数后，该多项式能唯一确定。即$(a_0,a_1,...a_n)$能唯一确定$f_n(x)$

- **点值形式**：当我们确定 $n$ 次多项式上的 $n+1$ 个点时，该多项式能唯一确定。即$\{(x_0,f_n(x+0)),(x_1,f_n(x_1)),...,(x_n,f_n(x_n)),(x_{n+1},f_n(x_{n+1}))\}$唯一确定$f_n(x)$

### 单位根

- 考虑方程 $x^n-1=0$ 在复数域上的解，不难发现，在复平面上画一个单位圆，以$(1,0)$为一个基准点，按逆时针将单位圆进行 $n$ 等分，得到的剩下 $n-1$ 个点是该方程的解，且其中第 $i$ 个点编号为$i$ ，记作$\omega_{n}^i$。
$$\omega_n^k = \cos {\frac{2k\pi}{n}}+i\sin{\frac{2k\pi}{n}}$$
- 不难发现，$\omega_n^0=\omega_n^n = 1$
- 单位根的性质
	- $\omega_{mn}^{mk} = \omega_n^k$
	- $\omega_n^{k+\frac{n}{2}} = -\omega_n^k$
	- $\omega_n^{n-k} = \overline{\omega_{n}^k}$

- 证明如下：
$$\omega_{mn}^{mk} =\cos {\frac{2mk\pi}{mn}}+i\sin{\frac{2mk\pi}{mn}}=\cos {\frac{2k\pi}{n}}+i\sin{\frac{2k\pi}{n}}=\omega_n^k$$
$$\omega_n^{k+\frac{n}{2}} =\omega_n^k \times\omega_n^{\frac{n}{2}} =\omega_n^k\times (\cos {\pi}+i\sin{\pi})=-\omega_n^k$$
$$\omega_n^{n-k} = \cos {\frac{2(n-k)\pi}{n}}+i\sin{\frac{2(n-k)\pi}{n}}$$
$$=\cos {(2\pi -\frac{2k\pi}{n})}+i\sin{(2\pi-\frac{2k\pi}{n})} =\cos {\frac{2k\pi}{n}}-i\sin{\frac{2k\pi}{n}} =\overline{w_n^k}$$

##  快速傅里叶变换FFT

- 将多项式 $f_n(x) = \sum a_ix^i$ 化为点值形式
- 设$n=2^k=k$，即第一个大于等于它的 $2$ 的整数次幂(高次项系数可以看成0)，将多项式分为偶数幂和奇数幂两部分
$$f_n(x) = (a_0+a_2x^2+...+a_{n-2}x^{n-2}+a_nx^n) + (a_1x+a_3x^3+...+a_{n-1}x^{n-1})$$
$$f_n(x) = (a_0+a_2x^2+...+a_{n-2}x^{n-2}+a_nx^n) +x(a_1+a_3x^2+...+a_{n-1}x^{n-2})=G(x^2)+xH(x^2)$$

- 当$x = \omega_n^k$ 时
$$f_n(x) = G(\omega_n^{2k}) + \omega_n^kH(\omega_{n}^{2k})=G(\omega_{\frac{n}{2}}^{k}) + \omega_n^kH(\omega_{\frac{n}{2}}^{k})$$
- 当$x=\omega_n^{k+\frac{n}{2}}$ 时
$$f_n(x) = G(\omega_n^{2k+n}) + \omega_n^{k+\frac{n}{2}}H(\omega_{n}^{2k+n})=
G(\omega_n^{2k}) - \omega_n^{k}H(\omega_{n}^{2k}) =G(\omega_{\frac{n}{2}}^{k}) - \omega_n^kH(\omega_{\frac{n}{2}}^{k})$$
- 故我们只需求出$G(\omega_{\frac{n}{2}}^{k})$，$H(\omega_{\frac{n}{2}}^{k})$ 就可以$O(1)$求出$f(\omega_n^k)$，$f(\omega_n^{k+\frac{n}{2}})$，
- 而$G(\omega_{\frac{n}{2}}^{k})$，$H(\omega_{\frac{n}{2}}^{k})$可以通过以上步骤进行递归求解$G(\omega_{\frac{n}{4}}^{k})$，$H(\omega_{\frac{n}{4}}^{k})$...不断缩小问题规模直到$G(\omega_1^{k}) = H(\omega_1^k)=1$，复杂度$O(\log n)$，因此总复杂度为$O(n\log n)$，就可以求出所有$f(\omega_n^k)$

## 快速傅里叶逆变换

- 将多项式的点值表示转回**系数表示**，这个转换的过程被称为**离散傅里叶逆变换**（**IDFT**）。

我们知道
$$f(x_i) = \sum_{k=0}^n a_kx_i^k (i=1,2,...,n)$$
- 写成矩阵形式$y_i = f(x_i) = f(\omega_n^i)$
$$\begin{gathered}\begin{bmatrix}y_0\\y_1\\y_2\\y_3\\\vdots\\y_{n-1}\end{bmatrix}=\begin{bmatrix}1&1&1&1&\cdots&1\\1&\omega_n^1&\omega_n^2&\omega_n^3&\cdots&\omega_n^{n-1}\\1&\omega_n^2&\omega_n^4&\omega_n^6&\cdots&\omega_n^{2(n-1)}\\1&\omega_n^3&\omega_n^6&\omega_n^9&\cdots&\omega_n^{3(n-1)}\\\vdots&\vdots&\vdots&\vdots&\ddots&\vdots\\1&\omega_n^{n-1}&\omega_n^{2(n-1)}&\omega_n^{3(n-1)}&\cdots&\omega_n^{(n-1)^2}\end{bmatrix}\begin{bmatrix}a_0\\a_1\\a_2\\a_3\\\vdots\\a_{n-1}\end{bmatrix}\end{gathered}$$
由于
$$x^n-1 = (x-1)(1+x+...+x^{n-2}+x^{n-1})$$
当$x=1$时, 
$$(1+x+...+x^{n-2}+x^{n-1})=n$$
其他情况下当$x^n-1=0$时
$$(1+x+...+x^{n-2}+x^{n-1})=0$$
所以有
$$\begin{bmatrix}1&(\omega_n^{n-i})^1&(\omega_n^{n-i})^2&\cdots&(\omega_n^{n-i})^{n-1}\end{bmatrix}\begin{bmatrix}1\\(\omega_n^j)^1\\(\omega_n^j)^2\\\vdots\\(\omega_n^j)^{n-1}\end{bmatrix}=\sum_{k=0}^{n-1}(\omega_n^{n-i+j})^k=\left\{\begin{array}{ll}n&i=j\\0&i\neq j\end{array}\right.$$

所以
$$\begin{bmatrix}1&1&1&\cdots&1\\1&(\omega_n^1)^1&(\omega_n^1)^2&\cdots&(\omega_n^1)^{n-1}\\1&(\omega_n^2)^1&(\omega_n^2)^2&\cdots&(\omega_n^2)^{n-1}\\\vdots&\vdots&\vdots&\ddots&\vdots\\1&(\omega_n^{n-1})^1&(\omega_n^{n-1})^2&\cdots&(\omega_n^{n-1})^{n-1}\end{bmatrix}$$
的逆矩阵为（容易验证）
$$\frac1n\begin{bmatrix}1&1&1&\cdots&1\\1&(\omega_n^{n-1})^1&(\omega_n^{n-1})^2&\cdots&(\omega_n^{n-1})^{n-1}\\1&(\omega_n^{n-2})^1&(\omega_n^{n-2})^2&\cdots&(\omega_n^{n-2})^{n-1}\\\vdots&\vdots&\vdots&\ddots&\vdots\\1&(\omega_n^1)^1&(\omega_n^1)^2&\cdots&(\omega_n^1)^{n-1}\end{bmatrix}$$
即
$$\frac{1}{n}\begin{bmatrix}1&1&1&\cdots&1\\1&(\overline{\omega_n^1})^1&(\overline{\omega_n^1})^2&\cdots&(\overline{\omega_n^1})^{n-1}\\1&(\overline{\omega_n^2})^1&(\overline{\omega_n^2})^2&\cdots&(\overline{\omega_n^2})^{n-1}\\\vdots&\vdots&\vdots&\ddots&\vdots\\1&\overline{(\omega_n^{n-1}})^1&(\overline{\omega_n^{n-1}})^2&\cdots&(\overline{\omega_n^{n-1}})^{n-1}\end{bmatrix}$$

- **结论**：将一个多项式在分治的过程中乘上的单位根变为其共轭复数，分治完的每一项除以 $n$ 即可得到原多项式的每一项系数。

## 倍增迭代实现

### 位逆序置换

- 我们每一次都会把整个多项式的奇数次项和偶数次项系数分开，一直分到只剩下一个系数。
- 我们可以先「模仿递归」把这些系数在原数组中「拆分」，然后再「倍增」地去合并这些算出来的值。
- 这个拆分过程可以使用位逆序置换实现
	- 原序列： $0,1,2,3,4,5,6,7$   $000,001,010,011,100,101,110,111$
	- 终序列： $0,4,2,6,1,5,3,7$   $000,100,010,110,001,101,011,111$
- 可以发现终序列是原序列每个元素的**二进制翻转**。
- 于是我们可以先把要变换的系数排在相邻位置，**从下往上**迭代。

```cpp
// 位逆序置换
void change(Complex y[], int len) {
    // i:000...01 , j: 10...000，保持镜像的同时交换对应位置的元素
    for (int i = 1, j = len / 2; i < len - 1; i++) {
        if (i < j) {
            swap(y[i], y[j]);
        }
        // 对j进行逆序加1，保持镜像
        // k 代表了 0 出现的最高位。j 先减去高位的全为 1 的数字，直到遇到了
        // 0，之后再加上即可。
        int k = len / 2;
        while (j >= k) {
            j -= k;
            k /= 2;
        }
        if (j < k) {
            j += k;
        }
    }
}
```

### 蝶形运算优化

使用位逆序置换后，对于给定的 $n, k$

- $G(\omega_{n/2}^k)$ 的值存储在数组下标为 $k$ 的位置，$H(\omega_{n/2}^k)$ 的值存储在数组下标为 $k + \dfrac{n}{2}$ 的位置。
- $f(\omega_n^k)$ 的值将存储在数组下标为 $k$ 的位置，$f(\omega_n^{k+n/2})$ 的值将存储在数组下标为 $k + \dfrac{n}{2}$ 的位置。
- 因此合并过程可以直接在数组下标为 $k$ 和 $k + \frac{n}{2}$ 的位置进行覆写，而不用开额外的数组保存值。此方法即称为 **蝶形运算**

借助蝶形运算完成所有段长度为 $\frac{n}{2}$ 的合并操作：

1. 令段长度为 $s = \frac{n}{2}$
2. 同时枚举序列 $\{G(\omega_{n/2}^k)\}$ 的左端点 $l_g = 0, 2s, 4s, \cdots, N-2s$ 和序列 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7 "\{H(\omega_{n/2}^k)\}") 的左端点 $l_h = s, 3s, 5s, \cdots, N-s$
3. 合并两个段时，枚举 $k = 0, 1, 2, \cdots, s-1$，此时 $G(\omega_{n/2}^k)$ 存储在数组下标为 $l_g + k$ 的位置，$H(\omega_{n/2}^k)$ 存储在数组下标为 $l_h + k$ 的位置；
4. 使用蝶形运算求出 $f(\omega_n^k)$ 和 $f(\omega_n^{k+n/2})$，然后直接在原位置覆写。

```cpp
void FFT(Complex y[],int len,int inv) {
    change(y,len);
    // 合并长度(2~len)
    for(int h=2;h<=len;h<<=1) {
        // 当前单位根的间隔
        Complex wn(cos(2*PI/h),sin(inv*2*PI/h));
        // 合并
        for(int j=0;j<len;j+=h) {
            Complex w(1,0);
            for(int k=j;k<j+h/2;k++) {
                Complex u = y[k];
                Complex t = w* y[k+h/2];
                y[k] = u+t;
                y[k+h/2] = u-t;
                w=w*wn;
            }
        }
    }
    if(inv==-1) {
        for(int i=0;i<len;i++) {
            y[i].x /= len;
        }
    }
}
```

## 例题

- [P3803 【模板】多项式乘法（FFT） - 洛谷](https://www.luogu.com.cn/problem/P3803)
```cpp
```cpp
#include <bits/stdc++.h>
#define ll long long
// #define int long long
#define IOS \
    ios::sync_with_stdio(false); \
    cin.tie(nullptr);
#define endl '\n'
int const inf = 0x3f3f3f3f;
ll const infll = 0x3f3f'3f3f'3f3f'3f3f;
using namespace std;
const double PI = acos(-1.0);
// ifstream fin("input.txt");
// ofstream fout("output.txt");
// #define fin cin
// #define fout cout

struct Complex {
    double x, y;

    Complex(double _x = 0, double _y = 0) {
        x = _x, y = _y;
    }

    Complex operator+(Complex const &b) {
        return Complex(x + b.x, y + b.y);
    }

    Complex operator-(Complex const &b) {
        return Complex(x - b.x, y - b.y);
    }

    Complex operator*(Complex const &b) {
        return Complex(x * b.x - y * b.y, x * b.y + y * b.x);
    }
};

// 位逆序置换
void change(Complex y[], int len) {
    // i:000...01 , j: 10...000，保持镜像的同时交换对应位置的元素
    for (int i = 1, j = len / 2; i < len - 1; i++) {
        if (i < j) {
            swap(y[i], y[j]);
        }
        // 对j进行逆序加1，保持镜像
        // k 代表了 0 出现的最高位。j 先减去高位的全为 1 的数字，直到遇到了
        // 0，之后再加上即可。
        int k = len / 2;
        while (j >= k) {
            j -= k;
            k /= 2;
        }
        if (j < k) {
            j += k;
        }
    }
}

void FFT(Complex y[],int len,int inv) {
    change(y,len);

    // 合并长度(2~len)
    for(int h=2;h<=len;h<<=1) {
        // 当前单位根的间隔
        Complex wn(cos(2*PI/h),sin(inv*2*PI/h));
        // 合并
        for(int j=0;j<len;j+=h) {
            Complex w(1,0);
            for(int k=j;k<j+h/2;k++) {
                Complex u = y[k];
                Complex t = w* y[k+h/2];
                y[k] = u+t;
                y[k+h/2] = u-t;
                w=w*wn;
            }
        }
    }
    if(inv==-1) {
        for(int i=0;i<len;i++) {
            y[i].x /= len;
        }
    }
}

int n,m;
const int N = 1<<22;
Complex a[N],b[N];
int ans[N];

signed main() {
    IOS
    cin>>n>>m;
    n+=1,m+=1;
    int len=1;
    while(len<n+m-1) len<<=1;
    for(int i=0;i<n;i++) {
        cin>>a[i].x;
    }
    for(int i=0;i<m;i++) {
        cin>>b[i].x;
    }

    FFT(a,len,1);
    FFT(b,len,1);
    for(int i=0;i<len;i++) {
        a[i] = a[i]*b[i];
    }
    FFT(a,len,-1);
    for(int i=0;i<len;i++) {
        ans[i] = int(a[i].x +0.5);
    }

    len = n+m-1;
    for(int i=0;i<len;i++) cout<<ans[i]<<" ";

	// fin.close(),fout.close();
	return 0;
}
```

