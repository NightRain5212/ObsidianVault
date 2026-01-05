## 汇编语言

- CPU的工作是执行很多的指令，这些指令是CPU执行的基本操作
- 不同的CPU有不同的处理器架构和不同的指令集
- 适用于处理器或一类处理器的特定指令集称为指令集架构
- 如`arm,x86,IBM power` ......

- 寄存器：汇编语言没有变量，汇编操作数通常是寄存器(处于处理器内核和计算单元附近，速度快)。
- 在汇编语言中，每个语句称为指令，每一行汇编语言最多包含一条指令。

## 指令集体系（ISA）

- **ISA**: *Instruction Set Architecture* 指令集架构
- **指令集架构**定义了一套软件控制中央处理器（CPU）的抽象方法，包含了[基本数据类型](https://zh.wikipedia.org/wiki/%E8%B3%87%E6%96%99%E5%9E%8B%E5%88%A5 "资料类型")、指令集、[寄存器](https://zh.wikipedia.org/wiki/%E5%AF%84%E5%AD%98%E5%99%A8 "寄存器")、[寻址模式](https://zh.wikipedia.org/wiki/%E5%AF%BB%E5%9D%80%E6%A8%A1%E5%BC%8F "寻址模式")、[存储体系](https://zh.wikipedia.org/w/index.php?title=%E5%AD%98%E5%82%A8%E4%BD%93%E7%B3%BB&action=edit&redlink=1 "存储体系（页面不存在）")、[中断](https://zh.wikipedia.org/wiki/%E4%B8%AD%E6%96%B7 "中断")、[异常处理](https://zh.wikipedia.org/wiki/%E5%BC%82%E5%B8%B8%E5%A4%84%E7%90%86 "异常处理")以及外部[I/O](https://zh.wikipedia.org/wiki/I/O "I/O")。指令集架构包含一系列的[opcode](https://zh.wikipedia.org/wiki/Opcode "Opcode")即操作码（[机器语言](https://zh.wikipedia.org/wiki/%E6%A9%9F%E5%99%A8%E8%AA%9E%E8%A8%80 "机器语言")），以及由特定处理器执行的基本命令。
- ISA的存在，使得软件和硬件的开发可以相互独立（解耦）
- **指令集 (Instruction Set)**：CPU能够理解和执行的所有命令的集合。
    - **操作类型**：包括算术运算（如`add`, `sub`）、逻辑运算（`and`, `or`）、数据传输（`load`, `store`）、控制流（`beq`分支, `jal`跳转）等。
    - **指令编码**：每条指令如何被编码成二进制的0和1（即机器码），包括操作码（opcode）、功能码（funct3/7）、寄存器地址、立即数等字段的格式。
- **寄存器 (Registers)**：CPU内部可供程序员（通过汇编语言）直接访问的高速存储单元的集合。ISA定义了：
    - **数量**：有多少个通用寄存器（如RISC-V有32个，x86有16个）。
    - **名称与用途**：每个寄存器的名称（如`x0`, `a0`, `sp`, `ra`）及其在**调用约定（Calling Convention）**中的建议用途。
    - **位数**：寄存器的宽度，如32位或64位。
- **数据类型与内存模型 (Data Types & Memory Model)**：
    - CPU可以直接操作的基本数据类型，如字节（8位）、半字（16位）、字（32位）、双字（64位）以及浮点数。
    - **字节序（Endianness）**：在内存中，多字节数据是按大端（Big-endian）还是小端（Little-endian）顺序存储。
- **寻址模式 (Addressing Modes)**：指令可以用来指定其操作数（数据）位置的方式。例如：
    - **立即数寻址**：操作数直接编码在指令中。
    - **寄存器寻址**：操作数在寄存器中。
    - **基址+偏移量寻址**：操作数的地址是某个寄存器的值加上一个偏移量，这是访问内存中变量和数组成员最常用的方式。
    - **PC相对寻址**：操作数的地址是程序计数器（PC）的值加上一个偏移量，`auipc`指令就是这种模式的体现，对位置无关代码至关重要。
- **特权等级与异常处理 (Privilege Levels & Exception Handling)**：
    - 定义了不同的CPU工作模式，如用户模式（User Mode）和更高权限的监管者/内核模式（Supervisor/Kernel Mode），以保护操作系统不受用户程序的干扰。
    - 定义了当发生意外事件（如除以零、非法内存访问）或外部中断时，CPU应该如何响应，即**异常和中断**的处理机制。


## Risk-V

- RiskV有32个寄存器，物理寄存器(`x0~x31`)
- `x0`总是存储0值。
- 在32位系统中，一个字是32位，4个字节。

### 调用规定

- Register Name(s) Usage
- x0/zero Always holds 0
- ra Holds the return address
- sp Holds the address of the boundary of the stack
- t0-t6 Holds temporary values that do not persist after function calls
- s0-s11 Holds values that persist after function calls
- a0-a1 Holds the first two arguments to the function or the return values
- a2-a7 Holds any remaining arguments


### add

- 执行加法操作
- 语法`add x1, x2, x3` （操作名称，目标寄存器，源寄存器1，源寄存器2）
- 等价于`x1 = x2 + x3`

### sub

- 执行减法操作
- `sub x3, x4, x5`
- 等价于`x3 = x4 - x5`

### 复合例子

- `a = b + c + d - e`
```riskV
add x10, x1, x2   #a_t = b + c
add x10, x10, x3  #a_t = a_t + d
sub x10, x10, x4  #a = a_t - e
```

### 立即数

- 汇编中的数值常量称为立即数。
- RiskV有用于对立即数和寄存器中的值操作的指令

### addi

- 用于立即数加法
- `addi x3, x4, 10`
- 等价于`x3 = x4 + 10`
- `addi x3,x4,-10`
- 等价于`x3 = x4 - 10`

### 零寄存器

- x0是一个特殊寄存器，只存储零值
- `add x3,x4,x0`
- 等价于`x3 = x4`
- 然而`add x0,x3,x4`将不会做任何事

### 内存

- 当CPU进行读写操作时，从内存中读取数据，将数据存储到内存中。
- 内存中数据的布局
	- 内存被组织成字，在RiskV中，一个字长32位。
	- 将四个字节组织成字的方法：
		- 在内存中存储字中的字节时，按小端序存储
		- 一个字中最低有效字节获得这个字中的最小地址。
		- 字的地址与最低有效字节地址相同。
- RiskV遵循小端字节序。字节序只管理字节在内存中的存储顺序。

### Load Word(lw)

- lw从内存加载数据(字)到寄存器
```c
int A[100];
g = h + A[3];
```
将其转化成汇编
- x15为基指针，指向`A[0]`。
- 偏移量以字节为单位，三个字是12个字节
- 偏移量必须是常量。
- 数据流：从右往左。
```RISKV
lw x10, 12(x15) # x10 = A[3]
add x11,x12,x10 # g = h + A[3]
```

### Store Word(sw)

- 从寄存器存储字到内存(将寄存器的值移动到由基指针和偏移量寻址的内存位置)
```c
int A[100];
A[10] = h + A[3]
```
 - 数据流：从左往右
```riskV
lw x10,12(x15)   # t = A[3]
add x10,x12,x10  # t = t + h
sw x10,40(x15)   # A[10] = h + A[3]， offset = 10 * 4 = 40
```

### Load Bytes & Store Bytes

- 以字节为单位进行内存读写。
- 命令为`lb`，`sb`，格式与`lw`，`sw`相同
- 将字节复制到寄存器时，为了保留带符号整数的符号，会将字节中的最高有效位复制扩展到所有高位，即符号扩展。
- 如果不想进行可以使用`lbu`。(不进行符号扩展，只用0填充)
- Ex
```riskV
addi x11,x0,0x3f5
sw x11,0(x5)
lb x12,1(x5)
```
- 请写出x12存储的情况
	- x11: 00 00 03 f5
	- x5：00 00 03 f5 （在内存中）
	- x12: 00 00 00 03  (偏移一个字节读取并符号扩展)

### 分支命令

#### beq & bne

- Branch if equal。
- `beq reg1,reg2,L1`
- 比较两个寄存器的内容，如果相等则跳转至标签L1，否则执行下一条指令
- `bne` : 当内容不相同时分支 branch if not equal

#### blt & bge

- `blt`：Branch if less than （小于时分支）
- `bge`：Branch if greater than or equal （大于等于时分支）
- 无符号版本：`bltu`，`bgeu`

#### 无条件分支 jump

- `j label`：直接跳转至标签label处

#### 一些例子

 - if语句
```c
if(i == j)
	f = g + h
```

```riskv
	bne x13,x14,Exit  # if i != j : j Exit
	add x10,x11,x12   # f = g + h
Exit:
```

- if-else
```c
if(i==j)
	f = g + h
else
	f = g - h
```

```riskv
bne x13,x14,Else
add x10,x11,x12
j Exit           # don't forget
Else: sub x10,x11,x12
Exit:
```

- for
```c
int A[20];
int sum = 0;
for(int i=0;i<20;i++)
	sum+=A[i];
```

```riskv
add x9,x8,x0    # x9=&A[0]
add x10,x0,x0   # sum
add x11,x0,x0   # i
addi x13,x0,20  # x13,final value of i
Loop:
	bge x11,x13,Done
	lw x12,0(x9)    # x12,A[i]
	add x10,x10,x12 # sum
	addi x9,x9,4    # &A[i+1]
	addi x11,x11,1  # i++
	j Loop
Done:
```

### bit-operation

- `and x5,x6,x7 # x5 = x6 & x7`
- `andi x5,x6,3 # x5 = x6 & 3`
- 类似的，还有`or`按位或运算，`xor`按位异或运算

### Logical Shifting

- 逻辑移位:
	- 逻辑左移和逻辑右移：`sll`,`srl`,
	- 立即数：`slli`,`srli`
- `slli x11,x12,2  #x11 = x12 << 2`

- 执行有符号数时，一般进行算数移位
	- 算术右移：`sra`，`srai`
	- 算术右移：用符号位复制空出的高位

### 汇编到机器码

- 用文本编写的汇编文件会传递给汇编器，生成机器码目标文件，加上预编译的目标文件库，通过链接器将目标文件和库文件链接起来，输出可执行的机器码文件。(存储在内存中)
- 程序计数器(特殊寄存器)：位于处理器内部，存储下一条指令的字节地址。

### 特殊指令

- `mv`：`mv  rd,rs = addi rd,rs,0`
- `li`：`li  rd,13 = addi rd,x0,13`
- `nop` ：`nop = addi x0,x0,0`

### 函数调用

- 调用函数的六个步骤：
	- 将**参数**放置在函数可以访问的位置
	- 传递控制权给函数
	- 获取函数所需的**局部存储资源**
	- 执行与函数相关的任务
	- 将**返回值**放置在调用代码可以找到的位置，并**恢复寄存器**到以前的状态，**释放局部存储**
	- **返回控制权**给起点，程序接管控制权
- 参数寄存器：a0~a7（x10~x17），其中a0,a1保存返回值
- ra：保存返回地址
- s0~s1（x8~x9），s2~s11(x18~x27) 保存寄存器

#### 例子

```c
int sum(int x,int y) {
	return x+y;
}
```

```
Address
1000  mv a0,s0           # x=a
1004  mv a1,s1           # y=b
1008  addi ra,zero,1016  # ra=1016
1012  j sum              # jump to sum
1016                     # next
...
2000 sum: add a0,a0,a1
2004 jr ra               # 返回到ra
```

#### Jump and Link（jal）

- `jal sum`
- 等价于`addi ra,zero,1016`&`j sum`
- `jal`会自动将返回地址保存在ra中

#### Jump Register（jr）

- 无条件跳转至寄存器所存储的地址`jr ra`，跳转到返回地址
- `ret = jr ra`

#### `jal rd, Label`

- **功能**: 跳转并链接（Jump and Link）
    
- **操作**: 将下一条指令的地址（即 `PC + 4`）存入目标寄存器 `rd`，然后跳转到 `Label` 指定的地址。

#### `jalr rd, rs, imm`

- **功能**: 跳转并链接寄存器（Jump and Link Register）
    
- **操作**: 将下一条指令的地址（即 `PC + 4`）存入目标寄存器 `rd`，然后跳转到 `rs + imm` 指定的地址

#### stack

- 函数调用时需要有内存空间存储旧值，即堆栈。
- 栈是后进先出(LIFO)的结构
- 堆栈指针SP(x2)，始终指向栈顶
- 堆栈从内存空间的顶部开始向下增长。
	- 压栈时递减SP
	- 出栈时递增SP
- 栈帧：
	- 返回地址
	- 参数
	- 将要使用的局部变量的空间
- 栈帧是连续的内存块，sp告诉我们最后一个栈帧的位置
- 添加一个栈帧时，要对应的移动sp；过程结束时将sp移回。
#### example

```c
int leaf(int g,int h,int i,int j)
{
	int f;
	f = (g+h)-(i+j);
	return f;
}
```

```riskv
Leaf:
	# prologue
	addi sp,sp,-8  # 开辟局部空间
	sw s1,4(sp)    # 将数据写入堆栈
	sw s0,0(sp)

	add s0,a0,a1   # f=g+h
	add s1,a2,a3   # s1=i+j
	sub a0,s0,s1   # return value (g+h)-(i+j)

	# epilogue
	lw s0,0(sp)    # 恢复寄存器
	lw s1,4(sp)
	addi sp,sp,8   # 恢复栈顶指针
	jr ra # (ret)
```

#### 函数嵌套例子

- 寄存器分类：
	- 保存的寄存器：调用者可以依赖这些保持不变的值。(s0-s11)
	- 易失性/临时寄存器：调用者不能依赖这些值保持不变。(a0-a7,t0-t6)
#### 内存分配

- 自动变量：函数的局部变量，函数被调用时出现，退出时丢弃。
- 静态变量：直到程序退出前一直存在。
- 栈帧保存：返回地址，参数寄存器，保存的寄存器，局部变量。

- C语言内存分区：
	- 静态存储区：储存静态变量，全局变量
	- 堆：储存malloc分配的动态数据
	- 栈：储存局部变量
```c
int sumSquare(int x,int y)
{
	return mult(x,x)+y; //sumSquare为调用者,mult为被调用者
}
```

```riskv
# x-a0,y-a1
sumSquare:

# push
addi sp,sp,-8   # 开辟堆栈空间
sw ra,4(sp)     # 保存返回地址
sw a1,0(sp)     # 保存 y(a1将被覆盖)

mv a1,a0        # a1=a0 ,以调用mult(x,x) 
jal mult        # 调用mult

# pop
lw a1,0(sp)     # 恢复 y
add a0,a0,a1    # mult(x,x)+y
lw ra,4(sp)     # 恢复返回地址
addi sp,sp,8    # 恢复sp

jr ra
```

- RV32约定：
	- 堆栈从内存顶部开始，所有内存区域与16字节边界对齐(与RV128兼容)
		- `bfff_fff0`$_{hex}$
	- 32位程序位于内存底部(不是在最底部)。
		- `0001_0000`$_{hex}$
	- 静态数据段位于文本(程序)正上方，全局指针(gp)指向静态数据
		- `1000_0000`$_{hex}$
	- 堆：位于静态数据段之上，（向上增长）


### 指令的二进制表示

- 一个字长32位的情况下分为几个字段，每个字段都会告诉处理器一些关于指令的信息
- 有一个字段来描述它是哪种类型的指令，导致处理器内部不同的操作序列。
- 如：
	- R-格式：寄存器-寄存器相关运算；
	- I-格式：寄存器-立即数相关运算。
	- S-格式：存储
	- B-格式：分支
	- U-格式：长立即数
	- J-格式：jump

#### R格式布局

- 字段分布
	- 操作码(opcode)：占7位(0-6)
	- 目标寄存器(rd)：占5位(7-11)
	- funct3：占3位(12-14)
	- 源寄存器rs1：占5位(15-19)
	- 源寄存器rs2：占5位(20-24)
	- funct7：占7位(25-31)

| funct7 | rs2 | rs1 | funct3 | rd  | opcode | 字段  |
| ------ | --- | --- | ------ | --- | ------ | --- |
| 7      | 5   | 5   | 3      | 5   | 7      | 位长  |

- 其中,opcode,funct3,funct7决定执行的操作。
- 所有R类型指令的操作码都相同,0110011
- 例子：
	- `add x18,x19,x10`

| 0000000 | 01010   | 10011   | 000 | 10010  | 0110011    |
| ------- | ------- | ------- | --- | ------ | ---------- |
| add     | rs2=x10 | rs1=x19 | add | rd=x18 | Reg-Reg OP |
![[r.png]]
#### I格式布局


| 字段  | imm | rs1 | funct3 | rd  | opcode |
| --- | --- | --- | ------ | --- | ------ |
| 位长  | 12  | 5   | 3      | 5   | 7      |
- 立即数范围$[-2048,2047]$
- 立即数总是进行符号扩展到32位
- 例子:
	- `adii x15,x1,-50`

| 111111001110 | 00001 | 000 | 0111  | 0010011 |
| ------------ | ----- | --- | ----- | ------- |
| imm=-50      | rs1=1 | add | ra=15 | I-OP    |
![[i.png]]

#### Load

- 加载命令也是采用I格式布局

| imm    | rs1  | funct3 | rd   | opcode |
| ------ | ---- | ------ | ---- | ------ |
| offset | base | width  | dest | LOAD   |
- 例子：
	- `lw x14,8(x2)`

| 000000001000 | 00010 | 010 | 01110 | 0000011 |
| ------------ | ----- | --- | ----- | ------- |
| imm=+8       | rs1=2 | lw  | rd=14 | LOAD    |

![[l.png]]
#### S格式布局


| imm            | rs2 | rs1  | funvt3 | imm           | opcode |
| -------------- | --- | ---- | ------ | ------------- | ------ |
| offset\[11:5\] | src | base | width  | offset\[4:0\] | STORE  |
- 偏移量立即数仍由12为组成，被分割为7个高位，5个低位
- 例子：`sw x14,8(x2)`

| 0000000 | 01110  | 00010  | 010 | 01000  | 0100011 |
| ------- | ------ | ------ | --- | ------ | ------- |
| offset  | rs2=24 | rs1=12 | SW  | offset | STORE   |
- offset = 000000001000=8
![[s.png]]
#### B格式布局

- 如果不进行分支，则会运行下一条命令(PC=PC+4)
- 如果进行分支，则要计算相对于程序计数器的偏移量(PC=PC+imm\*4)（**NOT TRUE**）
- 其中imm可以是负数.
- 压缩指令集：使用16位长的指令集合。
- 为了实现精确跳转，只使用一种类型的分支指令，能够以两个字节为单位进行索引。
- 实际上跳转的计算是:**PC=PC+imm\*2**
- imm始终为偶数，最低有效位总为0.只需要12位存储13位的偏移量

| imm\[12\] | imm\[10:5\] | rs2 | rs1 | funct3 | imm\[4:1\] | imm\[11\] | opcode |
| --------- | ----------- | --- | --- | ------ | ---------- | --------- | ------ |
| 1         | 6           | 5   | 5   | 3      | 4          | 1         | 7      |

#### 长立即数（U格式）


| imm\[31:12\] | rd  | opcode |
| ------------ | --- | ------ |
| 20           | 5   | 7      |
- U格式提供了一种将缺失的20位立即数放入寄存器的方法
- U格式有两种操作数
	- LUI：加载上部立即数(将上部立即数加载到目标寄存器中，将最低12位重置为0)
	- AUIPC：将上部立即数添加到PC，并将结果存储在rd中
- eg
```riskv
lui x10,0x87654      # x10 = 87654000
addi x10,x10,0x321   # x10 = 87654321

lui x10,0xDEADB      # x10 = DEADB000   
addi x10,x10,0xEEF   # x10 = DEADAEEF
```
- 0xEEF存储在寄存器时，会进行符号扩展导致0xDEADB的B的半字节递减。
- 所以写入时，会先lui写入0xDEADC在addi这样存储的就是正确的值
- 伪指令`li`: `li x10,0xDEADBEEF # 实际上调用两个指令`
- AUIPC: 将上部立即数添加到PC的当前内容，并将结果存储在寄存器rd中。
- AUIPC对PC相对寻址很有用，可以将偏移量添加到PC中
- `Label: AUIPC x10,0 # 将Label所在地址保存到x10中`

#### J格式

| imm\[20\] | imm\[10:1\] | imm\[11\] | imm\[19:12\] | rd  | opcode |
| --------- | ----------- | --------- | ------------ | --- | ------ |
| 1         | 10          | 1         | 8            | 5   | 7      |

- imm偏移量始终为偶数,因为要跳转到16位或32位指令的开头，所以用20位存储21位的数字


#### JALR（I格式）


| imm\[11:0\] | rs1 | funct3 | rd  | opcode |
| ----------- | --- | ------ | --- | ------ |
| 12          | 5   | 3      | 5   | 7      |
- `jalr rd,rs,imm`
-  将下一条指令的地址（即 `PC + 4`）存入目标寄存器 `rd`，然后跳转到 `rs + imm` 指定的地址(`PC = rs + imm`)

