
## Number Representation

- 详见[[数字的表示]]

## C intro

### 编译&解释：

- C过程中：.c文件通过编译成.o文件，再与其他文件(库文件等)连接成.out文件
- 预处理器命令：以`#`号开头的命令
- 宏：`#define`. 进行文本替换，在大部分时有效，在替换有副作用的函数时可能会重复作用. eg.`#define min(x,y) ((x)<(y)?(x):(y))`
### 指针：

- 在C中，局部变量声明后未初始化则为垃圾值。
- 地址与值：数据存储在内存中，(相当于一个很大的[[C++/基础/数组|数组]])，每个值都存在其对应的地址处的内存中。
- 指针：[[C/指针|指针]]是用来存储地址的[[变量]]。在C中，参数传递为值传递，想要修改原变量的值则需使用指针。

> [!warning]
> 注意不要使用未初始化的指针！

- 空指针：NULL为空指针，空指针意味着它指向空(NULL/0)，不能读取或写入。
- 指针时用户与内存互动的一个桥梁。
- `sizeof`运算符可以告诉你一个类型占用多少的字节。
```c
#include <stdio.h>
void IncrementPtr(int *p) 
//值传递，传递的是指针的值，而不是指针的地址，无法修改指针变量
{
    p = p + 1;
}

int main()
{
    int A[3] = {50, 60, 70};
    int *q = A;
    IncrementPtr(q);
    printf("*q = %d\n", *q); //q指针实际上未增加
    return 0;
}
//output: *q = 50
```

```c
#include <stdio.h>
void IncrementPtr(int **h) 
//要修改指针的值，传递指针的地址，即为接受指向指针的指针，h为句柄
{
    *h = *h + 1;
}
int main()
{
    int A[3] = {50, 60, 70};
    int *q = A;
    IncrementPtr(&q);
    printf("*q = %d\n", *q);
    return 0;
}
//output: *q = 60
```

### 数组

- 见[[C/数组|数组]]。
- 注意数组边界问题。程序员要防止数组索引越界。
- segmentation error ：读写无法访问的内存
- bus error：对齐方式错误(内存存储的字对齐)
- 指针算数：`pointer+n=pointer+n*sizeof('whatever pointer is pointing to')`
- 数组名不是一个变量
```c
#include<stdio.h>
#include<stdlib.h>
void foo() {
    int *p, *q, x;
    int a[4];
    p = (int *) malloc(sizeof(int));
    q = &x;
    *p = 1;
    printf("*p:%u, p:%u, &p:%u\n", *p, p, &p);
    *q = 2;
    printf("*q:%u, q:%u, &q:%u\n", *q, q, &q);
    *a = 3;
    printf("*a:%u, a:%u, &a:%u\n", *a, a, &a);
    free(p);
}

int main(void)
{
    foo();
    return 0;
}
/* output:
*p:1, p:4279542432, &p:2711898848
*q:2, q:2711898844, &q:2711898856
*a:3, a:2711898864, &a:2711898864
*/
```

- 函数指针的例子
```c
#include<stdio.h>

int x10(int) , x2(int);
void mutate_map(int [], int n, int(*) (int)); //将函数应用于列表的每一个元素
void print_array(int [],int n);

int x2 (int n) {return 2*n;}
int x10(int n) {return 10*n;}

void mutate_map(int A[], int n, int (*fp) (int)) 
//fp是函数指针，指向接受一个整数，返回一个整数的函数
{
    for(int i=0;i<n;i++) {
        A[i] = (*fp) (A[i]); //解引用指针调用函数
    }
}

void print_array(int A[], int n)
{
    for(int i=0;i<n;i++) {
        printf("%d ", A[i]);
    }
    printf("\n");
}

int main(void)
{
    int A[] = { 3, 1, 4 } , n = 3;
    print_array(A,n);
    mutate_map(A, n, &x2); //传递函数的地址，因为函数指针存储的是地址
    print_array(A,n);
    mutate_map(A, n , &x10); //传递函数的地址
    print_array(A,n);
}
/* output:
3 1 4
6 2 8
60 20 80
*/
```

### 内存管理

- 动态内存管理：
	- `malloc()`: 动态请求内存
		- 接受一个你想要的字节数，返回一个`void*`指针(指向通用空间)，还要进行强制类型转换，malloc不进行初始化
		- eg，`int * ptr = (int *) malloc(n*sizeof(int))`
	- `free()`: 动态释放内存
		- 必须进行**手动释放**，否则会内存泄漏
		- **内存不能被重复释放。**
	- `realloc()` : 动态调整内存
		- 接受malloc得到的原始指针，和你想要的字节数
		- eg, `ip = (int *) realloc(ip,20*sizeof(int))`
		- 调用前检测指针是否为空
- 全局变量：在函数外声明的变量，所有函数可访问。
- C内存池：
	- 静态存储区：全局变量存储空间，静态空间，在整个程序中永久存在。
	- 栈：局部变量存储的地方
	- 堆(不等于堆数据结构)：动态内存

- 栈：
	- 栈帧包括：返回地址，参数，为局部变量分配的空间
	- 栈是连续的内存块。
	- 栈是后进先出(LIFO)的结构。
	- 栈帧入栈时，栈指针会随之移动到栈顶。栈帧返回时，栈帧仍然存在于内存中，栈指针往回移动(返回地址)。

- malloc&free 实现：
	- 每个内存块都有一个头部(块的大小，指向下一个块的指针)，以循环链表存在。
	- 所有的空闲块都存储在这个循环链表中。
	- malloc：遍历搜索空闲列表，找到可以提供所需的内存，未找到则失败。
	- free：释放内存块时，检查相邻块是否空闲，对应的采取合并内存块等操作。
	- malloc选择策略：
		- best-fit：遍历整个链表，找到最接近所需的内存块。
		- first-fit：往下搜索，选择第一个足够大的内存块
		- next-fit：选择第一个足够大的内存块，记住位置，下一次接着往下搜索

- bad 内存管理策略：
	- 访问数组越界
	- 返回指向栈空间的指针（指向局部变量）
	- 使用释放过的内存
	- 使用`realloc`移动后的数据
	- 释放错误的内存
	- 重复释放内存
	- 丢失最初指针导致内存泄漏

