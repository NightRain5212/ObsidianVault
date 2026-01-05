
## 定义

- 设给定一个具有n个结点的序列 $F = k_0, k_1, ……, k_{n-1}$, 每个结点的权重均为正整数，分别是$w_{(k_0)},w_{(k_1)}, …, w_{(k_{n-1})}$。构造一棵以 $k_0, k_1, ……, k_{n-1}$作为叶子结点的二叉树T，二叉树T的带权路径长度 WPL是树中所有叶结点的带权路径长度之和，
-  $l_i$是从根结点到权重$w_{(k_i)}$的叶子结点的路径长度（路径所含的边数）。
$$WPL=\sum_{i=0}^{n-1}w_il_i$$
- Huffman树：给定一组叶结点权重，由此构建的所有带权二叉树中，带权路径长度WPL最小的二叉树称为哈夫曼树，又称最优二叉树。

## 性质

- Huffman树是**满二叉树**
- 给定一组叶结点权重，存在Huffman树，权重最小和次小的叶结点在树中最下层并且互为兄弟结点。
- Huffman树中，如果两个叶结点的权重值不同，则权重值小的叶结点在树中的层数一定不小于权重值大的叶结点。

## 构建算法

- 每次选出节点中权值最小的两个节点，合成一个新节点，新节点权值为两节点权值之和，新节点的左右孩子分别为这两个节点，加入待选节点中，不断重复直到待选节点为空。
```cpp
node* buildHuffmanTree(vector<int>& w) {
	priority_queue<node*,vector<node*>,greater<>> pq;
	for(int i:w) {
		node* n=new node(i);
		pq.push(n);
	}
	while(pq.size()>1) {
		node* l=pq.top();pq.pop();
		node* r=pq.top();pq.pop();
		node* newnode = new node(l->val+r->val);
		newnode->left = l;
		newnode->right = r;
		pq.push(newnode);
	}
	return pq.top();
}
```

## Huffman编码

- 如果需要对一定字符组成的字符串进行编码，且找到最短的无二义性的编码可以使用霍夫曼树进行编码。
- 将字符串出现的字符组成字符集，且权值为对应的出现次数，构建Huffman树。
- 构建完后，从根节点出发，左分支记为0，右分支记为1，走到叶子节点得到的01序列即为该字符编码。