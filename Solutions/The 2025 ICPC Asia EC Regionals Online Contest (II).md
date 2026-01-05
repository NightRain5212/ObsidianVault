
# D

- [Problem - D - Codeforces](https://codeforces.com/gym/106072/problem/D)

## 题目背景
你有 $N$ 个"奥术巨兽"（Arcane Behemoths），每个巨兽有一个攻击值 $Aᵢ$ 。

## 操作规则
1. 你可以选择任意一个**非空子序列**的巨兽进行出售
2. 出售时，你按照某种顺序一个一个地卖出这些巨兽
3. **关键机制**：每当你卖出一个巨兽时，**剩余还未卖出的**巨兽（在同一个子序列中）的攻击值都会增加刚才卖出的那个巨兽的攻击值

## 子序列的"价值"定义
- 对于一个选定的子序列，通过**最优的卖出顺序**，让最后剩下的那个巨兽的攻击值达到最大
- 这个最大的攻击值就是这个子序列的"价值"

## 需要计算的内容
计算**所有非空子序列**的"价值"之和，然后对 $998244353$ 取模。

## 样例

- 输入
```
3
3
1 2 3
6
1 1 4 5 1 4
3
998244350 998244351 998244352
```
- 输出
```
27
1266
998244328

```

## 输入输出
- 输入：多组测试数据，每组给出 N 和攻击值数组 A
- 输出：所有非空子序列的价值之和模 998244353

## 题解

- 注意到对于任意一个数组$[a_1,a_2,a_3,...,a_n]$,从小到大排序后，依次从后开始往前删除最后一个元素，直到只剩一个值，则此时该数组价值最大，且排序后原数组对总价值的贡献系数为：$1,1,2,4,8,...,2^n$ 
- 计算单个元素对数组总价值的贡献为：$P_i\times a_i$，其中
$$P_i=\left\{\begin{matrix} 
  1,i=1 \\  
  2^{i-2},i\ge2 
\end{matrix}\right. $$
- 因此可以得到数组的总价值计算公式
$$\sum_{i=1}^{n} P_ia_i$$
- 考虑单个元素对所有子数组的贡献，即考虑第$i$个元素在子数组中作为第$j$位的贡献。
- 在子数组中前$j-1$个数要从原数组的前$i-1$个数中选，共$\binom{i-1}{j-1}$种可能，考虑到后面的$n-i$个数都有可能进入子序列或不进两种选择，共$2^{n-i}$种可能。因此可以得到贡献计算公式为:
$$第i个元素在子数组中作为第j位的贡献=(\binom{i-1}{j-1}P_j\times 2^{n-i})a_i$$
- 所以考虑单个元素对所有子数列的贡献系数：
$$\sum_{j=1}^{i} \binom{i-1}{j-1}P_j\times2^{n-i}$$
$$=2^{n-i}[1+\binom{i-1}{1}+\binom{i-1}{2}\times2)+\binom{i-1}{3}\times 2^2+...]$$
- 考虑化简，由于这个形式类似二项式展开，故构造
$$(2+1)^{i-1} = \sum_{k=0}^{i-1}\binom{i-1}{k}2^k=[1+\binom{i-1}{1}\times2 + \binom{i-1}{2}\times 2^2+...]$$
- 所以化简得到单个元素对最终答案的贡献系数为
$$\frac{3^{i-1}+1}{2}\times2^{n-i}$$
- 所以总答案即可得出
$$\sum \frac{3^{i-1}+1}{2}\times2^{n-i} \times a_i$$
- **注意**：**在模运算中计算除法不能直接做除法！！！要乘以它的乘法逆元！！！**

- 实例AC代码
```cpp
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);
#define endl '\n' 
const int inf = 0x3f3f3f3f;
const ll infll = 0x3f3f3f3f3f3f3f3f;
const double PI = acos(-1.0);
using namespace std;
//ifstream fin("input.txt");
//ofstream fout("output.txt");
//#define cin fin
//#define cout fout

const int mod = 998244353;
// 快速幂
int power(int a,int b,int mod) {
    int base = a % mod;
    int ans = 1;
    while(b) {
        if(b&1) {
            ans = (ans*base) % mod;
        }
        base = (base*base) % mod;
        b>>=1;
    }
    return ans;
}


signed main()
{
    IOS
    int T;cin>>T;
    while(T--) {
        int n;cin>>n;
        vector<int> a(n);
        for(int &i:a) cin>>i;
        sort(a.begin(),a.end());
        int ans=0;
        for(int i=1;i<=n;i++) {
	        // 乘法逆元
            int inv2 = power(2,mod-2,mod);
            int p = (((power(3,i-1,mod)+1)*inv2)%mod)
		            *(power(2,n-i,mod))%mod;
            ans = (ans + (p*a[i-1])%mod ) %mod;
        }
        cout<<ans<<endl;
    }
    //fin.close(),fout.close();
    return 0;
}
```

# C

- [Problem - C - Codeforces](https://codeforces.com/gym/106072/problem/C)

## 题意

有三个学生正在练习一个包含S个问题的题目集。每个问题属于七种类型之一，描述了哪些学生能解决它。

七种类型分别对应只有学生1能解（F1）、只有学生2能解（F2）、学生1和2能解（F3）、只有学生3能解（F4）、学生1和3能解（F5）、学生2和3能解（F6）、三个学生都能解（F7）。保证F1到F7的和等于S。

现在需要将每个问题分配给恰好一个能解决它的学生，目标是使三个学生解题数量的最小值尽可能大。输出这个最大可能值。

## 题解

- 采用二分答案的思路，将解出最优解转换为判断可行性的问题。
- 不难得到答案的范围$[0,s]$
- 关键是`check`函数的编写，当每个人的解答数至少为`k`时，是否存在对应分配能满足条件。
- 注意到数组$F[i]$的分配方法与其二进制码有关,所以分配方案可以用二进制掩码表示。
```
数组    位掩码    分配方法
F[1] = 1(001) -> 1
F[2] = 2(010) -> 2
F[3] = 3(011) -> 1,2
F[4] = 4(100) -> 3
F[5] = 5(101) -> 1,3
F[6] = 6(110) -> 2,3
F[7] = 7(111) -> 1,2,3
```
- 当分配方法对应的掩码为$t$时，只要能进行分配的题目数量大等于分配人数数量乘以$k$，就存在对应的分配方法，即有解。
- 而不难发现掩码$t$对应的分配人数数量为$t$的二进制码中1的数量，即`popcount(t)`
- 如$t=3$时，要对$1,2$位同学进行分配，可作为题目来源的$\{F_i\}$为$\{F_1,F_2,F_3,F_5,F_6,F_7\}$
- 所以判定成功条件为
$$\sum F_i (其中F_i是可作为题目来源的集合)\ge popcount(t)\times k$$
## AC代码

```cpp
#include <bits/stdc++.h>
#define ll long long
//#define int long long
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);
#define endl '\n' 
const int inf = 0x3f3f3f3f;
const ll infll = 0x3f3f3f3f3f3f3f3f;
const double PI = acos(-1.0);
using namespace std;
//ifstream fin("input.txt");
//ofstream fout("output.txt");
//#define cin fin
//#define cout fout

int popcount(unsigned x) {
    int cnt=0;
    while(x) {
        if(x&1 == 1) cnt++;
        x>>=1;
    }
    return cnt;
}


bool check(int k,int f[]) {
	// t为位掩码
    for(unsigned t=1;t<8;t++) {
        int sum = 0;
        // 计算题目来源总数
        for(unsigned s = 1;s<8;s++) {
            if((s&t) > 0) {
                sum+=f[s];
            }
        }
        // 判定条件
        if(sum < popcount(t) * k) {
            return false;
        }
    }
    return true;
}

signed main()
{
    IOS
    int t;cin>>t;
    while(t--) {
        int s;cin>>s;
        int f[8];
        for(int i=1;i<=7;i++) cin>>f[i];
        int l=0;int r = s;
        while(l+1!=r) {
            int mid = (l+r)>>1;
            if(check(mid,f)) {
                l=mid;
            }else {
                r=mid;
            }
        }
        cout<<l<<endl;
    }
    //fin.close(),fout.close();
    return 0;
}

```
