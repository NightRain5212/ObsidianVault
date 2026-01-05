
ST 表（Sparse Table，稀疏表）,是用于解决 **可重复贡献问题** 的数据结构。

ST 表基于 [倍增](https://oi-wiki.org/basic/binary-lifting/) 思想，可以做到 $\Theta(n\log n)$ 预处理，$\Theta(1)$回答每个询问。但是不支持修改操作。

**可重复贡献问题** 是指对于运算$opt$，满足 $x\space opt\space x=x$，则对应的区间询问就是一个可重复贡献问题。例如，最大值有$\max(x,x)=x$，$gcd$ 有 $gcd(x,x)=x$，所以 RMQ 和区间 GCD 就是一个可重复贡献问题。

参考：[ST 表 - OI Wiki](https://oi-wiki.org/ds/sparse-table/#st-%E8%A1%A8)

## 实现

对于`RMQ`问题：
`st[i][j]`，指的是在序列的第$i$项，向后$2^j$个元素所包含序列间的最大值。
$$st[i][j] = max[i,i+2^j-1]$$
转移方程
$$st[i][j] = max(st[i][j-1],st[i+2^{j-1}][j-1])$$
区间长度$j$的范围：$[0,\log n]$，$log$是以2为底的对数向下取整。
当区间长度$j$确定时，左端点$l \in [1, n-2^j+1]$

### 预处理
```cpp
lg[1]=0;
for(int i=2;i<=n;i++) lg[i]=lg[i>>1]+1;
for(int i=1;i<=n;i++) cin>>a[i];

for(int i=1;i<=n;i++) st[i][0]=a[i];
// 先枚举区间长度，再枚举左端点
for(int j=1;j<=lg[n];j++) {
	for(int i=1;i<=n-(1<<j)+1;i++) {
		st[i][j] = max(st[i][j-1],st[i+(1<<(j-1))][j-1]);
	}
}
```

### 区间查询

查询$[L,R]$的最大值时，**将该区间分成 两个ST表可直接维护的小区间**，然后二者求最值即可。
分成的两个小区间为$[L,L+2^k-1]$,$[R-2^k+1,R]$，其中$k=log (R-L+1)$ 
$k$是$L$能覆盖的最大值即$L+2^k-1 = R$，$k = log(R-L+1)$
所以有
$$max[L,R] = max([L,L+2^k-1][R-2^k+1,R])=max(st[L][k],st[R-2^k+1][k])$$
```cpp
int l,r;cin>>l>>r;
int k = lg[r-l+1];
cout<<max(st[l][k],st[r-(1<<k)+1][k])<<"\n";
```

## 例题

[P3865 【模板】ST 表 && RMQ 问题 - 洛谷](https://www.luogu.com.cn/problem/P3865)

```cpp
#include <bits/stdc++.h>
#define LL long long
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);
#define endl '\n'
using namespace std;

const int N = 1e5+5;
int n,m;
int st[N][20];
int lg[N];
int a[N];

int main()
{
    IOS
    cin>>n>>m;
    lg[1]=0;
    for(int i=2;i<=n;i++) lg[i]=lg[i>>1]+1;
    for(int i=1;i<=n;i++) cin>>a[i];

    for(int i=1;i<=n;i++) st[i][0]=a[i];
    for(int j=1;j<=lg[n];j++) {
        for(int i=1;i<=n-(1<<j)+1;i++) {
            st[i][j] = max(st[i][j-1],st[i+(1<<(j-1))][j-1]);
        }
    }

    while(m--) {
        int l,r;cin>>l>>r;
        int k = lg[r-l+1];
        //[l,r]-> [l,l+2^k-1] & [r-2^k+1,r] ,k=lg[r-l+1];
        cout<<max(st[l][k],st[r-(1<<k)+1][k])<<"\n";
    }
    return 0;
}
```

