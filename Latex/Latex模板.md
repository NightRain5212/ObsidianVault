
### 注释

- Latex文件注释使用`%` ,来注释一行的内容，生成pdf时不会显示

### 命令，特殊符号

- latex中`\`代表一个特殊符号

### 普通文本

- 标题，摘要，正文，图表都是普通文本

### 正文

- 设定区域
	- `\documentclass{}` 和`\usepackage{}` 为设定区域，规定论文格式，导入依赖包等
- 正文区域
	- 在`\begin` 和`\end ` 之间的区域
	- 所有最终在PDF显示的可见内容均在此区域添加。
	- 在输入论文之前，我们要先输入论文的基本内容，题目，摘要，关键字等等
- 各级标题
	- chapter
	- section
	- subsection
	- subsubsection
- 常见命令
	- 换行:`\\`
	- 分段:`\par`
	- 分页命令:`\newpage`
	- 首行缩进:`\setlength{\parindent}{长度}`
- 