# 1

uCore 中的段页式存储管理方案与理论课中讲述的方案有何不同？

- ucore有用于模拟用户进程的虚拟内存空间`vma`，`mm`用以管理一个进程的所有`vma`数据结构
- `mm_struct`
```c
struct mm_struct {
    list_entry_t mmap_list;        // 双向链表头，链接所有属于一个进程的vma
    struct vma_struct *mmap_cache; // 当前使用的vma，支持访问的局部性
    pde_t *pgdir;                  // 指向对应的页表
    int map_count;                 // vma数量
    void *sm_priv;                 // 用于链接记录访问情况的链表头（用于置换算法）
};
```
- `vma_struct`
```c
// vma虚拟内存空间（连续的虚拟内存空间）
struct vma_struct {
    struct mm_struct *vm_mm; // 虚拟内存空间所属的进程
    uintptr_t vm_start;      // 开始地址  
    uintptr_t vm_end;        // 结束地址
    uint32_t vm_flags;       // 访问权限
    list_entry_t list_link;  // 双向链表（从小到大排列）
};
```
- `vm_flags`相关宏
```c
// 可读
#define VM_READ                 0x00000001
// 可写
#define VM_WRITE                0x00000002
// 可执行
#define VM_EXEC                 0x00000004
```

- 段页式存储管理：一个进程的虚拟地址空间被分为若干个`vma`段，它们由`mm_struct`进行统一管理。对于`vma`中的虚拟地址，通过二级页表进行映射，映射到物理地址。
- 与理论的不同之处：
	- 理论上分段是根据程序逻辑分成，代码段，数据段，堆栈段，等等，而ucore是分成若干个 `vma` 段，而同一个进程的若干个`vma`，由`mm_struct`统一管理。
	- 理论上分成的不同段由段寄存器储存段表，通过段选择子记录的段基址进行地址转化，找到该段对应的页表；而ucore在 bootasm.S 中设置：代码段：基址 0x0, 界限 0xFFFFFFFF数据段：基址 0x0, 界限 0xFFFFFFFF，由于段基址都是 0，实际上： 逻辑地址 = 线性地址 = 虚拟地址，直接通过二级页表映射到物理地址



# 2

- 试简述uCore中缺页异常的处理过程，它与理论课中讲述的过程有何不同？


## 缺页中断的产生原因

- 目标页面不存在。
- 目标页面不在内存中。
- 访问权限不符合。
## 处理

尝试缺页异常后，CPU将发生异常的线性地址装入寄存器CR2中，将错误码发送给中断服务例程，进行相应处理

```c
trap_dispatch(struct trapframe *tf) {
    char c;
    int ret;
    switch (tf->tf_trapno) {
    case T_PGFLT:  //page fault
        if ((ret = pgfault_handler(tf)) != 0) {
            print_trapframe(tf);
            panic("handle pgfault failed. %e\n", ret);
        }
        break;
    ...
}
```
调用`pgfault_handler`函数处理缺页中断
```c
static int
pgfault_handler(struct trapframe *tf) {
    extern struct mm_struct *check_mm_struct;
    print_pgfault(tf);
    if (check_mm_struct != NULL) {
	    // 处理缺页中断(mm_struct信息，错误码，错误地址)
        return do_pgfault(check_mm_struct, tf->tf_err, rcr2());
    }
    panic("unhandled page fault.\n");
}
```
- 处理缺页中断的函数
```c
int
do_pgfault(struct mm_struct *mm, uint32_t error_code, uintptr_t addr) {
    int ret = -E_INVAL;
    // 找到错误地址所在vma
    struct vma_struct *vma = find_vma(mm, addr);
    pgfault_num++;
	// 检查错误地址是否合法
    if (vma == NULL || vma->vm_start > addr) {
        cprintf("not valid addr %x, and  can not find it in vma\n", addr);
        goto failed;
    }
	// 处理访问权限引起的中断
    switch (error_code & 3) {
    default:
    case 2: 
	    // 程序尝试向一个虚拟地址写入，但该地址对应的页面不在内存中。
	    // vma必须具有可写权限
        if (!(vma->vm_flags & VM_WRITE)) {
            cprintf("do_pgfault failed: error code flag = write AND not present, but the addr's vma cannot write\n");
            goto failed;
        }
        break;
    case 1: 
	    // 程序尝试读取一个虚拟地址，但页面表显示页面已在内存中，异常
        cprintf("do_pgfault failed: error code flag = read AND present\n");
        goto failed;
    case 0: 
	    // 程序尝试读取或执行一个虚拟地址，但该地址对应的页面不在内存中。
	    // vma必须可读可执行
        if (!(vma->vm_flags & (VM_READ | VM_EXEC))) {
            cprintf("do_pgfault failed: error code flag = read AND not present, but the addr's vma cannot read or exec\n");
            goto failed;
        }
    }
    // 设置权限
    uint32_t perm = PTE_U;
    if (vma->vm_flags & VM_WRITE) {
        perm |= PTE_W;
    }
    addr = ROUNDDOWN(addr, PGSIZE);
    ret = -E_NO_MEM;
    pte_t *ptep=NULL;
    // 获取页表项，如果不存在则创建对应页表项（参数：1）
    if ((ptep = get_pte(mm->pgdir, addr, 1)) == NULL) {
        cprintf("get_pte in do_pgfault failed\n");
        goto failed;
    }
    // 如果页表项为空，说明是从未进入内存的情况
    if (*ptep == 0) { 
        if (pgdir_alloc_page(mm->pgdir, addr, perm) == NULL) {
            cprintf("pgdir_alloc_page in do_pgfault failed\n");
            goto failed;
        }
    }
    else { 
    //页面表项非空，页面被换出到磁盘
        if(swap_init_ok) {
            struct Page *page=NULL;
            // 从交换区换入页面
            if ((ret = swap_in(mm, addr, &page)) != 0) {
                cprintf("swap_in in do_pgfault failed\n");
                goto failed;
            }    
            // 重新建立映射
            page_insert(mm->pgdir, page, addr, perm);
            // 标记为可交换
            swap_map_swappable(mm, addr, page, 1);
            // 记录虚拟地址
            page->pra_vaddr = addr;
        }
        else {
            cprintf("no swap_init_ok but ptep is %x, failed\n",*ptep);
            goto failed;
        }
   }
   ret = 0;
failed:
    return ret;
}
```

- 与理论的不同之处在于
	- 理论上的缺页处理直接在页表上完成，是直接以页框为基本单位，而ucore则多出了`vma`，`mm`的数据结构对地址进行抽象。
	- ucore设置了专门的交换区域进行页面交换，将淘汰的页面数据存储在交换区中等待下次换入。


# 3

- 为了支持页面置换，在数据结构Page中添加了哪些字段，其作用是什么？

- 修改后的 `page`
```c
struct Page {
    int ref;                        // 被引用的数量
    uint32_t flags;                 // 表示该页框的状态(是否空闲)，最后两位01，10
    unsigned int property;          // 连续的空闲块数量
    list_entry_t page_link;         // 空闲块链表域
    
    list_entry_t pra_page_link;     // 页面置换算法所需（链表项）
    uintptr_t pra_vaddr;            // 页面置换算法所需（访问此虚拟地址发生缺页）
}
```

- 其中新增了
	- ` list_entry_t pra_page_link`：将Page串起来，建立用于页面置换算法维护的链表数据结构
	- `uintptr_t pra_vaddr`：记录了对应的虚拟地址（访问此虚拟地址发生了缺页）


# 4

- 请描述页目录项页(PDE)和页表目录项(PTE)的组成部分对uCore实现页面置换算法的潜在用处。请问如何区分未映射的页表表项与被换出页的页表表项？请描述被换出页的页表表项的组成结构。


- PTE（页表项）结构
```
// PTE的位域含义：
31-12位: 物理页框地址的高20位
11-9位:  保留位
8位:     G (Global) - 全局页面
7位:     PAT (Page Attribute Table) - 页属性
6位:     D (Dirty) - 脏页标志
5位:     A (Accessed) - 已访问
4位:     PCD (Cache Disable) - 缓存禁用
3位:     PWT (Write Through) - 透写
2位:     U/S (User/Supervisor) - 用户/管理员权限
1位:     R/W (Read/Write) - 读写权限
0位:     P (Present) - 存在位
```
- PDE（页目录项）结构
```
PDE的位域含义：
31-12位: 页表物理地址的高20位
11-9位:  保留位
8位:     G (Global) - 全局页面
7位:     PS (Page Size) - 页大小(0=4KB)
6位:     保留
5位:     A (Accessed) - 已访问
4位:     PCD (Cache Disable) - 缓存禁用
3位:     PWT (Write Through) - 透写
2位:     U/S (User/Supervisor) - 用户/管理员权限
1位:     R/W (Read/Write) - 读写权限
0位:     P (Present) - 存在位
```

- 其中，访问位，修改位，等记录页是否访问过，是否修改过的状态可以为页面置换算法提供信息支持。

# 未映射的页表表项与被换出页的页表表项区分


- 当页面从来没有映射过，则页表项32位全是0
- 如果是被淘汰的页面，则其前24位（对应`swap`区的`offset`）不是0，存在位为0.
- 被淘汰后的页面PTE组成：
	- `offset:24 bits`，`reserved: 7bits`，`pre:0 (1 bit)`
	- 前24位对应交换区的索引位置，最后一位存在位为0

# 5

- 按先进先出页面置换的思路维护一个链表（最新进入的页面放在表尾，最早进入的放在表头）
- 每次缺页中断时，检查最老页面的访问位位，如果是0，直接淘汰；如果是1，则将R位清零，并放到链表的尾端。
- 第二次机会算法就是寻找一个**在最近的时钟间隔内没有被访问过的页面**。如果所有的页面都被访问过了，该算法就简化为纯粹的FIFO算法。
```c
static int
_next_swap_out_victim(struct mm_struct *mm, struct Page ** ptr_page, int in_tick)
{
     list_entry_t *head=(list_entry_t*) mm->sm_priv;
     assert(head != NULL);
     assert(in_tick==0);

     // 找到链表尾部的页面，即最早进入的页面
     list_entry_t *le = head->prev;
     assert(head!=le);
     struct Page *p = le2page(le, pra_page_link);
     // 获取页表项
     pte_t *ptep = get_pte(mm->pgdir,p->pra_vaddr,0);
     // 找到最早的没有访问的页面
     while((*ptep & PTE_A)!=0) {
         list_entry_t *lep = le->prev;
         *ptep &= ~PTE_A; // 清除访问位
         list_del(le); // 删除链表中的表项
         list_add(head, le); // 添加到链表头部

         le = lep;
         p = le2page(le,pra_page_link);
         ptep = get_pte(mm->pgdir,p->pra_vaddr,0);
     }

     // 检查访问位
     assert((*ptep & PTE_A)== 0);
     // 最近未访问淘汰
     list_del(le);
     assert(p !=NULL);
     *ptr_page = p;
     return 0;
}
```