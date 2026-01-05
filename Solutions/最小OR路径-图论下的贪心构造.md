# 来源

[Problem #3462 - ECNU Online Judge](https://acm.ecnu.edu.cn/problem/3462/)

# 题意

给定一个有  $n$ 个点和 $m$ 条边的无向图，其中每一条边 $e_i$ 都有一个权值记为 $w_i$ 。

对于给出的两个点  $a$ 和 $b$ ，求一条 $a$ 到 $b$ 的路径，使得路径上的边权的 OR（位或）和最小，输出这个值。(也就是说，如果将路径看做边的集合 $e_1,e_2,...,e_n$，那么这条路径的代价为 $w_1 \:|\: w_2\: |\: ...\:|\: w_n$ ，现在求一条路径使得其代价最小，输出这个代价。 如果不存在这样的路径，输出$-1$ 。

- $2\leq n\leq10^4,0\leq m\leq10^6,0\leq w_i\leq2^{62}-1$
- $1\leq u_i,v_i,a,b\leq n,a\neq b$

# 题解

- *Dijkstra* 算法的核心假设是：如果 $A \to B$ 是最短路径，且 $B \to C$ 是边，那么 $A \to B \to C$ 的距离一定大于等于 $A \to B$。
- 在位运算`OR` 下，该贪心选择性质失效。
- 考虑按位构造答案，要使得答案尽量小则要求答案的高位尽量都是0.
- 从高位到低位，考虑该位能否为0：
	- 若该位为0，设则此时的最小代价为 $tmp$，最短路径中每一条边权值为$w_i$，
	- 由于 $w_1 | w_2 | ... |w_n = tmp$，以及或的性质(只有 `0|0=0` )，可以得到 $tmp$ 中的0位必须与$w_i$的0位位置相同。即，在二进制下，$tmp$ 中0出现的位置在 $w_i$ 中必须同样为0，此时 `w | tmp == tmp`
	- 因此将所有符合条件的边取出来，判断从$a$到$b$是否存在路径，若有则`ans`中该位可以为0，否则该位为1。

# AC代码

```cpp
#include <bits/stdc++.h>
#define ll long long
#define ull unsigned long long
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

int n,m,a,b;
const int N = 1e4+5;
// 定义边结构体
struct edge {
    int u,v;
    ull w;
};
vector<edge> edges;

// 并查集用于检查是否存在a,b之间的通路
int fa[N];

  
// 并查集操作
int findrt(int i) {
    if(fa[i]==i) {
        return i;
    }
    return fa[i] = findrt(fa[i]);
}

  
void unite(int x,int y) {
    int rx = findrt(x);
    int ry = findrt(y);
    fa[rx]=ry;    
}

bool isunited(int x,int y) {
    return findrt(x)==findrt(y);
}

  
// 合法性检查
bool check(ull t) {
	// 初始化并查集
    for(int i=1;i<=n;i++) {
        fa[i]=i;
    }
    // 遍历每条边
    for(auto [u,v,w]:edges) {
	    // 取出合法边，连接对应点
        if((w | t)==t) {
            unite(u,v);
        }
    }
    // 判断a，b是否连通
    return isunited(a,b);
}

signed main()
{
    IOS
    // 处理输入
    cin>>n>>m;
    for(int i=1;i<=m;i++) {
        int u,v;ull w;
        cin>>u>>v>>w;
        edges.push_back({u,v,w});
    }
    cin>>a>>b;
    // 初始化ans全为1
    ull ans = (1ULL<<62)-1;
	
	// 此时a,b不连通，不存在答案
    if(!check(ans)) {
        cout<<-1<<endl;
        return 0;
    }

   // 从高位到低位依次尝试能否变为0
    for(int i=61;i>=0;i--) {
	    // tmp为将ans的第i位变为0后的值
        ull tmp = ans ^ (1ULL << i);
        // 若存在最小代价位tmp时的a,b通路，则将该位设置为0
        if(check(tmp)) {
            ans = tmp;
        }
    }
    cout<<ans<<endl;
    //fin.close(),fout.close();
    return 0;
}
```