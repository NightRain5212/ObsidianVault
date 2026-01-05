
- 字典树用边来代表字母，而从根结点到树上某一结点的路径就代表了一个字符串。

## 模板

```cpp
struct trie {
	int nex[100010][26]; 
	// [节点数][字符集大小]
	//nex[i][j]：节点i通过j这条边指向下一个节点的编号
	int cnt; // 树的大小，也是下一个节点的编号
	bool exist[100010]; // 该结点结尾的字符串是否存在

	// 添加
	void insert(string s) {
		int p=0; // 根节点
		int n=s.size();
		for(int i=0;i<n;i++) {
			int c = s[i] - 'a';
			if(!nex[p][c]) nex[p][c] = ++cnt; // 如果不存在该节点则添加
			p = nex[p][c]; // 前往下一个节点
		}
		exist[p]=1; // 标记字符串存在
	}

	// 查找
	bool found(string s) {
		int p=0,n=s.size();
		for(int i=0;i<n;i++) {
			int c = s[i]-'a';
			if(nex[p][c]) p=nex[p][c];
			else return 0;
		}
		return exist[p];
	}
};
```

## 维护异或最大值/最小值

- 将数的二进制表示看做一个字符串，就可以建出字符集为 $\{0,1\}$ 的 trie 树。即01-trie.

```cpp
struct trie {
    int nex[N*L][2], cnt;
    
    void insert(int x) {
        int p = 0;
        for (int i = L - 1; i >= 0; i -= 1) {
            int c = (x >> i) & 1;
            if (!nex[p][c]) {
                nex[p][c] = ++cnt;
            }
            p = nex[p][c];
        }
    }
    
    int xormax(int a) {
        int ans = 0;
        int p = 0;
        for (int i = L - 1; i >= 0; i--) {
            int c = (a >> i) & 1;
            // 贪心，每次取与当前位相反的路径，尽可能多的构造1
            int choice = 1 - c;
            if (nex[p][choice]) {
	            // 如果节点存在则构造1，前往下一个节点
                ans |= (1 << i);
                p = nex[p][choice];
            } else {
	            // 否则构造0（无需操作），前往下一个节点
                p = nex[p][c];
            }
        }
        return ans;
    }

    int xormin(int a) {
        int ans = 0;
        int p = 0;
        for (int i = L - 1; i >= 0; i--) {
            int c = (a >> i) & 1;
            // 每次取与当前为相同的路径，尽可能多的构造0
            if (nex[p][c]) {
	            // 如果节点存在，前往下一个节点
                p = nex[p][c];
            } else {
	            // 如果节点不存在，构造1，前往下一个节点
                ans |= (1 << i);
                p = nex[p][1 - c];
            }
        }
        return ans;
    }
};
```

