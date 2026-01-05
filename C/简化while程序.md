```
ch = getchar()
while(ch != '\n')
{
	...
	ch = getchar()
}
```
可以简化为：

```
while((ch = getchar()) != '\n')
{	
	...
}
```

- 把两个行为合并成一个表达式，可以简化代码。
- #skill 合理使用圆括号组合子表达式。
- [[while]]