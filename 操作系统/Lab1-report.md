
# 为何要开启A20？ uCore OS是如何开启A20的？

- 开启A20之前，CPU只能通过20根地址线寻址，寻址空间为$2^{20}=1MB$
- 只有开启A20之后，CPU才能通过32根地址线寻址，寻址空间为$2^{32}=4GB$
- uCoreOS通过键盘控制器开启A20：等待键盘控制器不忙，向键盘控制器发送写输出端口的命令，等待键盘控制器不忙，向键盘控制器输出端口写入0xdf,开启A20
```
seta20.1:
	# 等待键盘控制器不忙
    inb $0x64, %al            
    testb $0x2, %al
    jnz seta20.1

	# 向键盘控制器发送写输出端口的命令
    movb $0xd1, %al           
    outb %al, $0x64           

seta20.2:
	# 等待键盘控制器不忙
    inb $0x64, %al            
    testb $0x2, %al
    jnz seta20.2

	# 向键盘控制器输出端口写入0xdf,开启A20
    movb $0xdf, %al           
    outb %al, $0x60           
```


# 试分析段描述符表（GDT ）的结构，说明段描述符中每个字段的含义以及作用。uCore OS是如何初始化GDT表的？

- 全局段描述符表有若干个段描述符组成。
```c
static struct segdesc gdt[] = {
    SEG_NULL,
    [SEG_KTEXT] = SEG(STA_X | STA_R, 0x0, 0xFFFFFFFF, DPL_KERNEL),
    [SEG_KDATA] = SEG(STA_W, 0x0, 0xFFFFFFFF, DPL_KERNEL),
    [SEG_UTEXT] = SEG(STA_X | STA_R, 0x0, 0xFFFFFFFF, DPL_USER),
    [SEG_UDATA] = SEG(STA_W, 0x0, 0xFFFFFFFF, DPL_USER),
    [SEG_TSS]    = SEG_NULL,
};
```
- 如代码所示：GDT由空段描述符，内核代码段描述符，内核数据段描述符，用户代码段描述符，用户数据段描述符，和TSS描述符组成
- 段描述符结构如下：
```c
struct segdesc {
    unsigned sd_lim_15_0 : 16;  // 段界限的低16位      
    unsigned sd_base_15_0 : 16; // 段基址的低16位
    unsigned sd_base_23_16 : 8; // 段基址的16-23位
    unsigned sd_type : 4;       // 段类型
    unsigned sd_s : 1;          // 描述符类型
    unsigned sd_dpl : 2;        // 段描述符DPL特权级  
    unsigned sd_p : 1;          // 段存在标志
    unsigned sd_lim_19_16 : 4;  // 段界限高4位  
    unsigned sd_avl : 1;            
    unsigned sd_rsv1 : 1;            
    unsigned sd_db : 1;          // 默认操作数大小
    unsigned sd_g : 1;           // 粒度    
    unsigned sd_base_31_24 : 8;  //段基址24-31位
};
```
- GDT初始化：
- 初始化GDT时，建立了三项段描述符,分别为空描述符，供内核与bootloader使用的代码段描述符和数据段描述符
```
gdt:
    SEG_NULLASM                                    
    SEG_ASM(STA_X|STA_R, 0x0, 0xffffffff)          
    SEG_ASM(STA_W, 0x0, 0xffffffff)                
```
- 然后将gdt的地址和大小保存在gdtdesc中
```
gdtdesc:
    .word 0x17                                      # sizeof(gdt) - 1
    .long gdt                                       # address gdt
```
- 将gdtdesc加载到gdtr寄存器中
```
lgdt gdtdesc
```

# 实模式和保护模式有何不同？uCore OS是如何使能和进入保护模式的？

- 实模式：1M的寻址，可以访问内存中所有的地址，没有特权级。
- 保护模式：4G的寻址，启用了分段和分页机制，定义了特权级别，实现了**内存管理与保护**
- 进入保护模式的过程：
	- 启用A20，初始化GDT表，将CR0寄存器PE位设置为1，跳转至程序入口运行。
- 使能保护模式：将CR0寄存器PE位设置为1
```
movl %cr0, %eax
orl $CR0_PE_ON, %eax
movl %eax, %cr0
```


# Bootloader是如何利用ELF文件头的相关属性加载和运行uCore OS Kernel的？

- BootLoader先读取ELF文件头
```c
readseg((uintptr_t)ELFHDR, SECTSIZE * 8, 0);
```
- 然后检验ELF文件头的`e_magic`字段，判断是否合法
```c
if (ELFHDR->e_magic != ELF_MAGIC) {
	goto bad;
}
```
- 然后获取程序头表的地址和大小，得出程序头表的头指针和末尾指针
```c
struct proghdr *ph, *eph;
// load each program segment (ignores ph flags)
ph = (struct proghdr *)((uintptr_t)ELFHDR + ELFHDR->e_phoff);
eph = ph + ELFHDR->e_phnum;
```
- 然后遍历程序头表的每一项，将每一段读取到对应的内存区域
```c
for (; ph < eph; ph ++) {
	readseg(ph->p_va & 0xFFFFFF, ph->p_memsz, ph->p_offset);
}
```
- 最后跳转至程序入口，执行
```c
((void (*)(void))(ELFHDR->e_entry & 0xFFFFFF))();
```


# 分析中断描述符表（IDT）的结构，说明中断描述符中每个字段的含义以及作用。 uCore OS是如何实现中断机制的？

- IDT由256个中断描述符组成
```c
static struct gatedesc idt[256] = {{0}};
```
- 中断描述符如下：
```c
struct gatedesc {
    unsigned gd_off_15_0 : 16;    // 偏移量0-15位
    unsigned gd_ss : 16;          // 段选择子，指定中断处理程序所在的代码段
    unsigned gd_args : 5;         // 
    unsigned gd_rsv1 : 3;         //
    unsigned gd_type : 4;         // 门类型
    unsigned gd_s : 1;            //  
    unsigned gd_dpl : 2;          // 描述符特权级
    unsigned gd_p : 1;            // 段存在标志
    unsigned gd_off_31_16 : 16;   // 偏移量16-31位  
};
```
- 中断机制实现：
	- 首先操作系统先建立IDT表和GDT表。建立中断号与中断服务例程的映射关系。
	- 当中断产生时，系统通过中断号在IDT查找对应的中断描述符，取得对应的段选择子和偏移量，再通过段选择子在GDT表找到中断服务例程的基地址加上偏移量，得到入口地址。
	- 保存当前状态到堆栈(EFLAGS,CS,EIP,ERROR_CODE,如果有更换特权级还要加上ESP,SS)，跳转至入口地址执行程序，执行完毕后弹栈，还原至中断处理之前的状态。


#  利用uCore OS的中断机制，编程实现一个计时器（ 秒表 ），可以实现正计时和倒计时。先选择计时模式，输入“A”为正计时，“B”为倒计时。若选择倒计时，则要先输入初始时间（秒数）。按“S”开始正（倒）计时，按“P”暂停正（倒）计时，按“C”继续正（倒）计时，按“E”停止正（倒）计时，暂停正（倒）计时或停止正（倒）计时时显示计时（剩余）时间值（秒数）。（时间精确到0.1秒）

- 实现思路如下

- 辅助变量：
```c
static bool isTiming=0; // 是否正在计时状态
static bool neg=0;      // 是否倒计时
static bool isInputNumber = 0; // 是否处于输入数字的状态
static int initsecond=0;       // 倒计时的初始秒数
int buffer[32];   // 输入数字时的缓冲区
int idx=0;        // 追踪输入到缓冲区的位置
```

- 处理键盘输入逻辑
```c
case IRQ_OFFSET + IRQ_KBD:
	c = cons_getc();
	cprintf("kbd [%03d] %c\n", c, c);
	// 判断输入的字符
	if(c == 'a') {
		// 初始化正计时器状态
		neg=0;
		ticks=0;
		isTiming = 0;
		cprintf("Timer Ready!\n");
	}
	if(c=='b') {
		// 初始化倒计时器状态
		neg=1;
		cprintf("Please enter a second number：\n");
		isInputNumber=1; // 进入输入数字的状态
		ticks=0;
		initsecond=0;
		// 恢复缓冲区游标
		idx=0;
		
		isTiming=0;
	}
	if(c == 's' && isTiming ==0) {
		// 进入计时状态，开始计时
		isTiming = 1;
		cprintf("Timer Start!\n");
	}
	if(c == 'p' && isTiming == 1) {
		// 退出计时状态，不清空状态
		isTiming = 0;
		cprintf("Timer Pause!\n");
	}
	if(isInputNumber ) {
		if(c=='\n') {
			// 结束输入状态
			isInputNumber=0;
			int i=0;
			// 计算初始秒数
			for(;i<idx;i++) {
				initsecond = initsecond*10 + buffer[i];
			}
			cprintf("\nCountdown set to %d seconds\n", initsecond);
		}
		else if(c>='0' && c<='9')
			// 将数字输入到缓冲区
			buffer[idx++]=c-'0';
	}
	break;
```

- 处理时钟更新逻辑

- 设置更新间隔
``` c
#define TICK_NUM 10 // 将100改成10（10ticks=0.1s）
```
- 处理更新逻辑
```c
case IRQ_OFFSET + IRQ_TIMER:
	// 如果处于计时状态更新时间戳
	if(isTiming) {
		ticks ++;
	}
	if (ticks!=0 && ticks % TICK_NUM == 0) {
		print_ticks();
	}
	break;
```
- 处理计时逻辑
```c
static void print_ticks() {
	int s;   // 计算有多少个0.1s
	int d,r; // 存储输出结果的整数部分和小数部分
	if(!neg) {
		// 处理正计时
		s =ticks/10;
		d=s/10;
		r=s%10;
	}else {
		// 处理倒计时
		// 初始秒数先计算总共有多少时间戳
		int tolticks = initsecond*100;
		// 时间到/超过 初始时间
		if(ticks>=tolticks) {
			isTiming = 0;
			cprintf("\rTimer end! 0.0s");
			return;
		}else {
			// 计算剩余多少时间
			s = (tolticks-ticks)/10;
			d=s/10;
			r=s%10;
		}
	}
	// 输出
	cprintf("\r %d.%d s",d,r);
```
