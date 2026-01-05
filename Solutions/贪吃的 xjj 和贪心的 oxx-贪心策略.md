
# 来源

[Problem #3496 - ECNU Online Judge](https://acm.ecnu.edu.cn/problem/3496/)

# 题意

oxx 与 xjj 终于到了 Xiamen，他们第一件事就是去吃当地著名的特产椰子饼。

他们共买了 $n$ 盒礼盒，第 $i$ 盒含 $a_i$ 块椰子饼。oxx 与 xjj 约定让 oxx 来分配这 $n$ 盒椰子饼。

为了讨好 xjj ，oxx 决定自己的椰子饼的总数要不大于 xjj 的总数。但是 oxx 知道贪吃的 xjj 会忍不住拆开一盒吃了，因此贪心的 oxx 希望： xjj 无论拆开哪一盒她自己的椰子饼后，oxx 拥有的椰子饼总数要不小于 xjj 剩余的椰子饼总数。oxx 想知道是否存在一个分配方案能够满足他的要求，如果存在输出 `Yes`，并输出方案，否则 `No`。

如果上述题意让你感到很困惑的话，我们要求的是 $S=a_1,a_2,...,a_n$ 这一多重集的一个非空子集$S' = a_{i_1},a_{i_2},...,a_{i_n}$ ，使得

$$Sun(S') \le Sum(S-S') \:\wedge\: (\forall t\in S-S' ,Sum(S')\ge Sum(S)-t) 
$$

记多重集 $Sum(S)$ 表示多重集  $S$ 所有元素的和。特别地， $Sum(\emptyset) = 0$。

更困惑了吗？Here we go.


# 题解

设总集合为$S$，oxx拿的集合为$T$，由条件显然有
$$Sum(T) \le Sum(S-T)$$
$$Sum(T) \ge Sum(S-T)-\min(S-T)$$
其中，$\min(S-T)$ 表示集合 $S-T$  中的最小元素。

显然尽可能地均分两堆可以逼近最优解。（贪心方向）

我们可以先构造两堆元素，从0开始。因为其中大的一堆中取出最小值后一定会小等于另一堆，所以我们将所有元素从大到小排序后依次考虑放到哪一堆（因为这样按顺序每次考虑的值一定是已经考虑的元素里的最小值）。

由于我们要尽可能地均分，所以每次贪心地往较小堆中放值即可，到最后较小堆就是oxx的选取集合，再检验条件即可。

# ac代码

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

int n;
const int N =1e6+5;
struct node
{
    int v;
    int idx;
} a[N];
int pre[N];

bool cmp(node& a,node& b) {
    return a.v<b.v;
}


signed main()
{
    IOS
    cin>>n;int s=0;

    for(int i=1;i<=n;i++) {
        cin>>a[i].v;
        a[i].idx=i;
        s+=a[i].v;
    }
    sort(a+1,a+1+n,cmp);

    if(n==2) {
        cout<<"Yes"<<endl;
        cout<<1<<endl;
        cout<<a[1].v<<endl;
        return 0;
    }    

    vector<int> aa,bb;
    ll sa=0,sb=0;
    ll mina=infll,minb=infll;
    for(int i=n;i>=1;i--) {
        if(sa<=sb) {
            sa+=a[i].v;
            if(a[i].v<mina) mina=a[i].v;
            aa.push_back(a[i].idx);
        }
        else {
            sb+=a[i].v;
            if(a[i].v<minb) minb=a[i].v;
            bb.push_back(a[i].idx);
        }
    }

    if(sa<=sb) {
        if(sa>= sb-minb) {
            cout<<"Yes"<<endl;
            cout<<aa.size()<<endl;
            for(int i:aa)cout<<i<<" ";
            cout<<endl;
        }else {
            cout<<"No";
        }
    }
    else {
        if(sb>=sa-mina) {
            cout<<"Yes"<<endl;
            cout<<bb.size()<<endl;
            for(int i:bb) cout<<i<<" ";
            cout<<endl;
        }else {
            cout<<"No";
        }
    }

    //fin.close(),fout.close();
    return 0;
}

```