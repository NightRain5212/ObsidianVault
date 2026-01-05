
- [Problem #3338 - ECNU Online Judge](https://acm.ecnu.edu.cn/problem/3338/)

## 题意

Alice 和 Bob 在玩积木游戏。

他们找到了 $n$ 块积木，这些积木都是正方体，棱长分别为 $a_1,a_2,...,a_n$。现在 Alice 和 Bob 要用这些积木垒两座高塔。他们想要这两座高塔的高度相等。问最大高度可能是多少？

摆放积木的顺序没有要求。两座高塔**不能**公用积木。

## 算法思想与设计

- 设状态 $dp[i]$ 为两高塔高度差为 $i$ 时的高度较低的塔的高度。
- 考虑状态转移，当考虑到棱长为 $a[j]$ 的积木时，分三种情况
	- 不使用该积木 $dp[i] = dp[i]$ 
	- 将该积木放在高的一侧 $dp[i+a[j]] = dp[i]$
	- 将积木放在矮的一侧
		- 若$a[j]<i$，$dp[i-a[j]]=dp[i]+a[j]$
		- 若$a[j] \ge i$，$dp[a[j]-i] =dp[i]+i$


## 伪代码

![nwpHM78ZqlhGE2z.png](https://s2.loli.net/2025/11/11/nwpHM78ZqlhGE2z.png)

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

const int N = 105;
const int M = 100005;
int n;
// 滚动数组优化，只保留上一层数组
int a[N],dp[2][M];


signed main()
{
    IOS
    cin>>n;int m=0;
    for(int i=1;i<=n;i++) {
        cin>>a[i];m+=a[i];
    }
    // 初始化为负无穷大表示不可达状态
    for(int j=0;j<=m;j++) {
        dp[0][j] =dp[1][j]= -inf;
    }
    
    dp[0][0]=0;
    // dp过程
    for(int i=1;i<=n;i++) {
        int cur = i&1;
        int lst = cur^1;
        // 初始化用来保存当前状态的数组
        for (int j=0; j<=m; j++) dp[cur][j] = -inf;

        for(int j=0;j<=m;j++) {
            if(dp[lst][j]!=-inf) {
                dp[cur][j] = max(dp[cur][j], dp[lst][j]);
                if (j + a[i] <= m) 
	                dp[cur][j+a[i]] = max(dp[cur][j+a[i]],dp[lst][j]);
                if(a[i]<=j) {
                    dp[cur][j-a[i]] = max(dp[cur][j-a[i]],dp[lst][j]+a[i]);
                }else {
                    dp[cur][a[i]-j] = max(dp[cur][a[i]-j],dp[lst][j]+j);
                }
            }
        }

    }
    cout<<dp[n&1][0];

    //fin.close(),fout.close();
    return 0;
}

```