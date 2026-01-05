
# G

- [Problem - G - Codeforces](https://codeforces.com/contest/2149/problem/G)
- 题意：
	- 给定一个长度为 `n` 的数组 `a[1..n]`，然后有 `q` 次询问。  每次询问给出一个区间 `[l, r]`，你需要找出**在这个区间中出现次数严格大于 (r - l + 1) / 3 的所有元素**。如果没有这样的元素，输出 `-1`；  如果有多个，按从小到大的顺序输出它们。
- 输入：
	- 第一行包含一个整数  **t (1 ≤ t ≤ 10⁴)** —— 表示测试用例的数量。
	- 每个测试用例的第一行包含两个整数  **n** 和 **q**  (**1 ≤ n, q ≤ 2 × 10⁵**)  ，分别表示记录的数量（数组长度）和查询的数量。
	- 第二行包含 **n 个整数**  **a₁, a₂, …, aₙ (1 ≤ aᵢ ≤ 10⁹)**  
	- 接下来有 **q 行**，每行包含两个整数  **l** 和 **r** (**1 ≤ l ≤ r ≤ n**)  
	- 保证所有测试用例中 **n 的总和** 与 **q 的总和** 不超过 `2 × 10⁵`。

- 首先想到桶计数，记录区间每一位数的出现次数，但是复杂度会超标，起码是$O(qn)$
- 题意是给定一个数据流，找出出现次数**超过某个比例阈值**（如超过总数的 1/k）的元素。这是一个频繁元素问题，因此采用**Misra–Gries 算法**来解决。
- 在此题中$k=3$，因此在每个区间内是答案的值不超过两个，即可以维护两个候选答案值。

## **Misra–Gries 算法**

- 使用**有限的内存（O(k))**，**单次扫描**整个数据流，**近似找出**所有出现次数 $> n/k$ 的元素。
- 算法思路：存储 **k-1 个候选元素** 和它们的计数。假设存储到哈希表`C(size=k-1)`中
- 遍历数据流每一个元素`x`：
	- 如果`x`在`C`中，`C[x]++`
	- 如果不在`C`中，若`C`不满，则添加这一个元素为候选，计数为1
	- 如果不在`C`中，且`C`满，则`C`中每一个元素计数减1。如果减为0，则移除它。
- 算法保证所有真实频率 > n/k 的元素一定在候选中
- 但是候选中可能含有“伪”频繁元素，需要二次验证

- 在此题，我们可以用线段树维护区间的两个候选值，合并线段树节点的过程实际上就是使用Misra–Gries 算法来合并。

- 最后还要验证查询结果是否有效：将该元素的出现的位置存入一个数组$L$，排好序，则其在区间$[l,r]$出现的次数为：$upperbound(L,r)-lowerbound(L,l) > n/k$

- 示例代码
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
double const PI = acos(-1.0);
using namespace std;
// ifstream fin("input.txt");
// ofstream fout("output.txt");
// #define cin fin
// #define cout fout

int const MAXN = 2e5 + 10;

int a[MAXN];

struct node {
    pair<int, int> p1, p2;

    node() {
        p1 = p2 = {0, 0};
    }
};

node merge(node &a, node &b) {
    node ans;
    vector<pair<int, int>> tmp;
    if (a.p1.second) {
        tmp.push_back(a.p1);
    }
    if (a.p2.second) {
        tmp.push_back(a.p2);
    }
    if (b.p1.second) {
        tmp.push_back(b.p1);
    }
    if (b.p2.second) {
        tmp.push_back(b.p2);
    }

    for (auto& [val, cnt]: tmp) {
        if (ans.p1.first == val) {
            ans.p1.second += cnt;
        } else if (ans.p2.first == val) {
            ans.p2.second += cnt;
        } else if (ans.p1.second == 0) {
            ans.p1 = {val, cnt};
        } else if (ans.p2.second == 0) {
            ans.p2 = {val, cnt};
        } else {
            int mind = min({ans.p1.second, ans.p2.second, cnt});
            ans.p1.second -= mind;
            ans.p2.second -= mind;
            cnt -= mind;
            if (cnt > 0) {
                if (ans.p1.second == 0) {
                    ans.p1 = {val, cnt};
                } else if (ans.p2.second == 0) {
                    ans.p2 = {val, cnt};
                }
            }
        }
    }
    return ans;
}

struct segTree {
    int n, N;
    vector<node> tr;

    void init(int n) {
        this->n = n;
        N = 1;
        while (N <= n + 1) {
            N <<= 1;
        }
        tr.resize(N << 1);
        for (int i = 1; i <= n; i++) {
            tr[N + i].p1 = {a[i], 1};
            tr[N + i].p2 = {0, 0};
        }
        for (int i = N - 1; i >= 1; i--) {
            tr[i] = merge(tr[i << 1], tr[i << 1 | 1]);
        }
    }

    node query(int l, int r) {
        node ans;
        for (l += N - 1, r += N + 1; l ^ r ^ 1; l >>= 1, r >>= 1) {
            if (~l & 1) {
                ans = merge(ans, tr[l ^ 1]);
            }
            if (r & 1) {
                ans = merge(ans, tr[r ^ 1]);
            }
        }
        return ans;
    }
} tree;

vector<pair<int,int>> idxs;

signed main() {
    IOS int t;
    cin >> t;
    while (t--) {
        idxs.clear();
        int n, q;
        cin >> n >> q;
        for (int i = 1; i <= n; i++) {
            cin >> a[i];
        }
        for (int i = 1; i <= n; i++) {
            idxs.push_back({a[i],i});
        }
        sort(idxs.begin(),idxs.end());
        tree.init(n);
        while (q--) {
            int l, r;
            cin >> l >> r;
            node ans = tree.query(l, r);
            int a = ans.p1.first, b = ans.p2.first;
            vector<int> res;
            if (a) {
                int c = upper_bound(idxs.begin(), idxs.end(), 
		                pair<int,int>(a,r)) -
                        lower_bound(idxs.begin(), idxs.end(), 
                        pair<int,int>(a,l));
                if (c > (r - l + 1) / 3) {
                    res.push_back(a);
                }
            }
            if (b) {
                auto& tmp = idxs[b];
                int c = upper_bound(idxs.begin(), idxs.end(), 
		                pair<int,int>{b,r}) -
                        lower_bound(idxs.begin(), idxs.end(), 
                        pair<int,int>{b,l});
                if (c > (r - l + 1) / 3) {
                    res.push_back(b);
                }
            }
            sort(res.begin(),res.end());
            if(res.empty()) cout<<-1;
            else {
                for(int i:res) cout<<i<<" ";
            }

            cout << endl;
        }
    }
    // fin.close(),fout.close();
    return 0;
}
```
- 时间复杂度：
	- 建树$O(n\log n)$
	- 合并节点$O(1\times \log n) = O(\log n)$
	- 查询$O(q\times \log n\times 1) = O(q\log n)$
	- 总复杂度$O((n+q)\log n)$
	- 