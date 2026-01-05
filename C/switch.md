- 形式
```
switch(number)
{
case1:statement1;
case2:statement2;
...
casen:statementn;
default:statement;

}
```
- 对表达式number求值，然后扫描case列表直到匹配为止，若没有匹配的则运行default行，
- break的作用：跳出switch语句，执行switch下一条语句。没有break就会从匹配标签语句一直执行到switch末尾。
- number的值必须为整型（char也行）
- 注意跟[[if]]的区别