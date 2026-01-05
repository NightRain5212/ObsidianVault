

- 来源：[Problem #5041 - ECNU Online Judge](https://acm.ecnu.edu.cn/problem/5041/)

Cuber QQ 想为 Quber CC 的生日准备一件礼物。他买了 $n$ 个套娃，第 $i$ 个套娃的长度、宽度、高度分别是$a_i,b_i,c_i$ ，重量是 $w_i$ 。当且仅当 $a_i < a_j$ 且 $b_i<b_j$ 且 $c_i<c_j$ 同时满足的时候, 第 $i$ 个套娃可以被装进第 $j$ 个套娃里。每一个套娃中最多装一个别的套娃。

Cuber QQ 想要给 Quber CC 送一个他所能组装成的最重的套娃（内部可能套了许多层），请你告诉他最重的套娃有多重。

## 算法思想与设计

- 这道题是三维偏序上的dp，若降成一维，就是带权重最长上升子序列最优化权重和的问题。
- 若给每一个套娃一个$dp$属性作为状态，表示以该套娃为最外层的最重套娃重量。
- 显然
$$dp[i] = \max {(dp[j]+w[j])}  (a_j <a_i\text{ and }b_j < b_i \text{ and } c_j <c_i )$$
- 而暴力转移的复杂度是$O(n^2)$，会超时，**考虑CDQ分治**。

- 先将所有套娃按 $a,b,c$ 依次为关键字排序，考虑更新区间$[l,r]$，将该区间分为两个部分$[l,mid],[mid+1,r]$，递归解决两个区间内部问题，在解决跨区间问题。
- 但是dp有一定的顺序，排序后从左往右dp，所以更新顺序应该是，先递归解决区间$[l,mid]$的问题，在用$[l,mid]$的更新后的状态更新区间$[mid+1,r]$，再递归解决区间$[mid+1,r]$
- 最关键的是用$[l,mid]$的更新后的状态更新区间$[mid+1,r]$这一步，已知左边部分的$a_L$一定小于右边的$a_R$，对两个区间分别按$b$排序后降维处理(类似于归并排序合并两个数组时)，两边$a$的相对大小（左小于右）不会变化，所以对于右边的一个元素$R$ ，只有左侧元素的$L_c \in [0,R_c-1]$时，可以转移，只要取得这个区间的最大值转移即可，树状数组维护区间最大值优化。
$$dp_R = \max {(dp_L +w[L])} (L_c < R_c)$$

## 伪代码

![pYw9RNXBojZy6c1.png](https://s2.loli.net/2025/11/11/pYw9RNXBojZy6c1.png)

## AC代码

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
const int N = 1e5+5;
int n;
struct node{
    int a,b,c,w;
    int dp;
} nodes[N],tmp[N];


bool cmp(node& x,node& y) {
    if(x.a != y.a) return x.a<y.a;
    if(x.b != y.b) return x.b<y.b;
    return x.c<y.c;
}

bool cmpb(node& x,node& y) {
    return x.b<y.b;
}

// 维护区间最大值的树状数组
struct BIT {
    vector<int> c;
    int n;

    void init (int _n) {
        n = _n;
        c.resize(n+5);
    };

    inline int lowbit(int x) {
        return x & -x;
    }

    void update(int x,int k) {
        for(int i=x;i<=n;i+=lowbit(i)) {
            c[i] = max(c[i],k);
        }
    }

    int query(int x) {
        int ans = 0;
        for(int i=x;i>0;i-=lowbit(i)) {
            ans = max(ans,c[i]);
        }
        return ans;
    }

    void clear(int x) {
        for(; x<=n; x+=lowbit(x)) c[x] = 0;
    }

} t;

void solve(int l,int r) {
    if(l>=r) return;
    int mid =(l+r)>>1;
    // 解决左半部分
    solve(l,mid);
    // 两边分别对b排序
    sort(nodes+l,nodes+1+mid,cmpb);
    sort(nodes+mid+1,nodes+1+r,cmpb);
    // 跨区间处理
    int i=l;
    for(int j=mid+1;j<=r;j++) {
        while(i<=mid && nodes[i].b < nodes[j].b) {
            if(nodes[i].a < nodes[j].a) t.update(nodes[i].c,nodes[i].dp);
            i++;
        }
        nodes[j].dp = max(nodes[j].dp,t.query(nodes[j].c-1)+nodes[j].w );
    }
    // 清空树状数组
    for(int k=l; k<i; k++) t.clear(nodes[k].c);
    // 恢复排序
    sort(nodes+l,nodes+1+r,cmp);
    // 处理右半部分
    solve(mid+1,r);
}


signed main()
{
    IOS
    cin>>n;
    // 由于数据较大进行离散化处理
    vector<int> allc;
    for(int i=1;i<=n;i++) {
        cin>>nodes[i].a>>nodes[i].b>>nodes[i].c>>nodes[i].w;
        nodes[i].dp = nodes[i].w;
        allc.push_back(nodes[i].c);
    }
    sort(allc.begin(),allc.end());
    allc.erase(unique(allc.begin(),allc.end()),allc.end());
    for(int i=1;i<=n;i++) {
        nodes[i].c = lower_bound(allc.begin(),allc.end(),nodes[i].c)
				         - allc.begin() + 1;
    }
	
	// 排序
    sort(nodes+1,nodes+1+n,cmp);
    int maxn = allc.size();
    t.init(maxn);
    // 进行dp
    solve(1,n);
    int ans = 0;
    // 获取答案
    for(int i=1;i<=n;i++) ans = max(ans,nodes[i].dp);
    cout<<ans<<endl;


    //fin.close(),fout.close();
    return 0;
}

```