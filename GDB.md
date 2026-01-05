
## 常见命令

- `(gdb) list`：查看源码
- `(gdb) break program.c:10`：在program.c文件第十行设置断点
- `(gdb) break main.c:20 if i == 5`:设置条件断点
- `(gdb) clear main.c:20`:清楚程序在20行的断点
- `(gdb) info breakpoints`: 查看断点
- `(gdb) run`：运行代码
- `(gdb) print var`：打印变量var的值
- `(gdb) watch var`: 观察变量var的值
- `(gdb) step`：逐步运行
- `(gdb) continue`： 继续运行
- `(gdb) quit`：退出
- `(gdb) catch throw`:设置一个捕捉点，当程序抛出异常时，它将暂停。
- `(gdb) whatis x`: 查看数据类型
- `(gdb) display x`：自动显示表达式的值，每次程序停止时都会显示。
- `(gdb) set variable x=5`：设置变量的值
- `(gdb) set args a1 a2 ...` ：设置参数
- `(gdb) info locals`：查看当前栈帧局部变量
- `(gdb)info args`: 查看当前栈帧参数
- 