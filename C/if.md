- 形式：
```
if(expression)
	statement
```
- if语句称为选择语句（分支语句）
- 若expression为真则执行statement，反之跳过statement.

# if else

- 形式
```
if (expression)
	statement1
else
	statement2
```
- 如果expression为真则执行statement1。如果expression为假则执行statement2。

# else if

- 形式
```
if(expression1)
	statement1
else if (expression2)
	statement2
...
else if (expressionN)
	statementN
```
- 如果没有花括号，else与最近的if匹配。（除非有花括号）

# if嵌套

- 在if语句中再次使用if，即为if嵌套