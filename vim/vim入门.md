
## Normal mode

- 光标移动
	- h：向左
	- j ：向下
	- k：向上
	- l ：向右
	- （不加N时默认为1）
	- N+k,N+j：向上（下）N行
	- N+w：向右移动N个单词（不加N时默认为1）
	- N+b：向左移动N个单词
	- $：移动到当前行最后
	- ^：移动到当前行开头
	- 0：移动到当前行的绝对最前面
	- shift+g：移动到文件末尾
	- gg：移动到文件开头
	- {：移动到下一个段落
	- N+G，N+gg，:N+enter :移动到第N行
- 查找特定词语
	- /word + enter . 
	- n ：到下一个match
	- shift+n ：到上一个match
- 撤回
	-  u：撤销操作
	- ctrl+r：撤回上一次撤销
- 剪切，复制，粘贴
	- dd：剪切当前行
	- yy：复制当前行
	- p：粘贴
- 替换
	- :%s/要替换词/替换目标词 + Enter
- 退出
	- :w+enter：保存
	- :q+enter：退出
	- :wq+enter：保存并退出
	- :q!+enter：强制退出
	- :wqa+enter：全部退出
## Insert mode

- 在该模式下可以对文件进行编辑
- i：进入insert mode
- esc：退出Insert
- shift+i：进入当前行的开头进入Insert
- shift+a：进入当前行的末尾进入Insert
- o：进入下一行，并进入Insert
- O：进入上一行，并进入Insert
- c + Nacigation：
	- c$：删除光标后所有内容（当前行），进入Insert
	- cw：删除光标后下一个词语,进入Insert
	- ciw：删除当前光标所在词，进入Insert

## Visual mode

- v：进入v-character
- shift+v：进入v-line模式
	-  j+>：调整行前缩进
	- ctrl+v：变成注释