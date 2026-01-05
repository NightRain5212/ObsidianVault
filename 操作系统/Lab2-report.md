# 1

## uCore如何探测物理内存布局，其返回结果e820映射结构是什么样的？分别表示什么？  
在ucore操作系统**启动BIOS后，进入保护模式之前**，需要进行内存布局探测，调用`e820h`中断获取内存信息，它会将信息储存在一个映射结果并返回，保存在地址`0x8000`处。

调用`e820h`中断探测内存布局的过程如下

```ass
probe_memory:
    movl $0, 0x8000   # 将表项数设置为0
    xorl %ebx, %ebx   # 将ebx寄存器设置为0
    movw $0x8004, %di # 设置第一个表项需要存放的地址位置
```
    
```
start_probe:
    movl $0xE820, %eax   # 设置E820中断号
    movl $20, %ecx       # 设置一个地址内存描述符的大小20字节
    movl $SMAP, %edx     # 设置edx为SMAP，用于E820中断调用
    int $0x15            # 发起0x15中断
    jnc cont             # 检查CF位是否为0
    
    # CF位不为0，错误处理：
    movw $12345, 0x8000  # 错误的表项数
    # 结束探测
    jmp finish_probe
    
# CF位为0，进行下一个地址内存描述符的探测
cont:
    addw $20, %di     # 设置下一个表项存放的地址位置（加一个表项的大小）
    incl 0x8000       # 将表项数递增加1
    cmpl $0, %ebx     # 比较ebx是否为0，如果为0探测结束，否则继续探测
    jnz start_probe   # 回到start_probe标签处
```

返回的映射结构如下
```c
struct e820map {
    int nr_map;   // 内存块数量=系统内存映射描述符表项数
    // 系统内存映射描述符表
    struct {
        uint64_t addr;  // 基地址
        uint64_t size;  // 大小
        uint32_t type;  // 类型
    } __attribute__((packed)) map[E820MAX]; 
};
```


# 2

## uCore中的物理内存空间管理采用什么样的方案？跟我们理论课中的哪个方案相似？有何不同之处？

ucore维护空闲内存块的数据结构：
```c
typedef struct {
    list_entry_t free_list;         // the list header
    unsigned int nr_free;           // # of free pages in this free list
} free_area_t;

struct list_entry {
    struct list_entry *prev, *next;
};
```
- 可见ucore使用一个带头节点的**双向循环链表**来维护空闲内存块。
- 与使用链表的内存管理方案类似。
- 不同之处在于：
	 - ucore使用的是双向循环链表，而课上方案是单向链表
	 - ucore是对分页后的空闲内存块进行单独管理，每一空闲块的大小固定为页面大小。单链表方案则是每一个节点可能是进程可能是空闲块按地址排序管理，每一节点的大小不是固定的。
	 - ucore是对块描述符`Page`进行管理，还维护了当前页框的状态，正在使用此页框的进程数，连续的空闲块数量等属性。
	 - 从`page`所属的页框号可以完成到物理地址的转化，由于页框大小固定，可以通过`property`属性计算块大小，而不必单独维护这两个(物理地址和块大小)属性。

# 3

## Page数据结构中每个字段含义与作用是什么？如何表示某物理块分配与否？字段property的作用是什么？如何表示 **property是否有效？**

```c
struct Page {
    int ref;                  // 有页表项引用了此页          
    uint32_t flags;           // 表示该页框的状态(是否空闲)，最后两位01，10
    unsigned int property;    // 以此页框开始的连续的空闲页框数量
    list_entry_t page_link;   // 双向循环链表指针域
};
```

- `flag`：用于确定该页框的状态，第0位为是否空闲的标志，第一位为`property`属性是否有效的标志
```c
#define PG_reserved                 0       
#define PG_property                 1       
```
- 当`flag`的`PG_reserved`位（第0位）为1表示该块被预留，非空闲，不可用；
- 反之，为0时表示该块没有被预留，是空闲块，可用。
- `property`字段的作用：如果该页面是连续空闲页的第一页，表示此页框开始的连续的空闲页框数量
- 当`flag`的`PG_property`位（第1位）为1表示`property`属性有效，反之无效。
- 看`flag`的最后两位判断页状态，`01`非空闲不可用，`10`空闲可用

# 4

## uCore现有代码已实现的物理块分配首次适应算法以及物理块回收算法有没有问题或者错误？如有的话，请简述相关的问题或者错误，并修改相应的代码。

物理块回收算法**错误部分**：只设置了回收的空闲块的首块的状态，而且只设置了`property`位，没有设置`reserved`位，后面的块状态未更新。
修改后代码为

```c
static void
default_free_pages(struct Page *base, size_t n) {
    assert(n > 0);
    assert(PageReserved(base));
    list_entry_t *le = &free_list;
    struct Page * p;
    while((le=list_next(le)) != &free_list) {
      p = le2page(le, page_link);
      if(p>base){
        break;
      }
    }
    //list_add_before(le, base->page_link);
    for(p=base;p<base+n;p++){
      list_add_before(le, &(p->page_link));
    }

    struct Page * pp;
	for(pp = base;pp<base+n;pp++) {
	  pp->flags = 0;
	  set_page_ref(pp,0);
	  ClearPageReserved(pp);
	  SetPageProperty(pp);
	  pp->property = (pp == base? n : 0);
	}
    
    p = le2page(le,page_link) ; 

    if( base+n == p ){
      base->property += p->property;
      p->property = 0;
    }
    le = list_prev(&(base->page_link));
    p = le2page(le, page_link);
    if(le!=&free_list && p==base-1){
      while(le!=&free_list){
        if(p->property){
          p->property += base->property;
          base->property = 0;
          break;
        }
        le = list_prev(le);
        p = le2page(le,page_link);
      }
    }
    nr_free += n;
    return ;

}
```



# 5
## 你认为现有的uCore OS物理块管理算法有没有进一步优化的可能？如有的话，请简要说明你的相关优化方案。

可能的物理块管理优化方案

- 建立类似于快表的物理块缓冲区。
- 将常用大小的页面块（如1页，2页，4页，等）存储在缓冲区里，获取时直接从缓冲区获取，不必再扫描空闲块链表。

## 设计方案

- 请求分配内存时：
	- 先检查分配内存的大小是否存在于缓冲区索引中。
	- 若存在对应的缓冲区且非空，取出一个返回。$O(1)$
	- 如果不存在对应的缓冲区，则使用页面分配算法找到合适的区域。
- 释放内存时：
	- 先检查释放内存的大小是否存在于缓冲区索引中。
	- 若存在且未满，将该内存块释放后放入对应的缓冲区中。这样后续的相同大小分配就能直接从缓存获取。
	- 否则就使用页面回收算法将其释放。


# 6

## get_pte、get_page函数的作用是什么？它们的输入与返回分别是什么？


##  `get_pte`函数结构

- 根据线性地址获取页表表项的函数：
- 输入：
	- 页目录`pgdir`
	- 线性地址`la`
	- 若对应的页表项不存在是否创建对应页表项`create`
- 返回：
	- 对应的页目录描述符地址
```c
pte_t *
get_pte(pde_t *pgdir, uintptr_t la, bool create) {
	// 获取页目录描述符
	// PDX(la):获取la的高十位，即页目录号
    pde_t *pdep = &pgdir[PDX(la)];
    // 查看存在位，对应的页表是否存在内存中
    if (!(*pdep & PTE_P)) {
	    // 不在内存中，则申请分配
        struct Page *page;
        if (!create || (page = alloc_page()) == NULL) {
            return NULL;
        }
        // 设置ref为1
        set_page_ref(page, 1);
        // 获取申请的物理地址
        uintptr_t pa = page2pa(page);
        // 对这个块初始化，即清零。
        memset(KADDR(pa), 0, PGSIZE);
        // 设置这个页表项（物理地址，低三位设置为111）
        // 设置：存在，可读写，用户可访问
        *pdep = pa | PTE_U | PTE_W | PTE_P;
    }
    // 返回页表表项的起始地址
    // PDE_ADDR(*pdep) 取出这个页目录项中的物理地址即页表索引
    // KADDR(PDE_ADDR(*pdep)) 获取页表索引的内核虚拟地址
    // &((pte_t *)KADDR(PDE_ADDR(*pdep))) 获取对应的页表初始地址
    //在对应的页表中根据la中的页表索引取出对应的页表项。
    // &((pte_t *)KADDR(PDE_ADDR(*pdep)))[PTX(la)] 
    return &((pte_t *)KADDR(PDE_ADDR(*pdep)))[PTX(la)];
}
```

##  `get_page` 

- 获取逻辑地址`la` 对应的物理块描述符（物理页面/页框）
- 输入：
	- 页目录`pgdir`
	- 线性地址`la`
	- 用于储存得到的页表项的指针`ptep_store`
- 返回：
	- 对应的块描述符`Page`
```c
struct Page *
get_page(pde_t *pgdir, uintptr_t la, pte_t **ptep_store) {
	// 首先获取la对应的页表项
    pte_t *ptep = get_pte(pgdir, la, 0);
    // 将找到的页表项指针储存
    if (ptep_store != NULL) {
        *ptep_store = ptep;
    }
    // 如果该块存在于内存中，返回对应的块描述符
    if (ptep != NULL && *ptep & PTE_P) {
        return pa2page(*ptep);
    }
    // 否则获取失败
    return NULL;
}
```

# 7

## 下次适应分配算法的实现

```c
static list_entry_t *rem = &free_list;

static struct Page *
next_fit_alloc_pages(size_t n) {
    assert(n > 0);
    if (n > nr_free) {
        return NULL;
    }
	if(list_empty(&free_list)) return NULL;
	// 如果rem指向链表头，则取第一项真正的节点。
	if(rem == &free_list) rem = list_next(rem);

    list_entry_t *le = rem, *len;
    struct Page *p = NULL;

    // 搜索合适的空闲块
    while (1) {
        p = le2page(le, page_link);
        if (p->property >= n) {
            break;
        }
        le = list_next(le);
        if (le == rem) {  // 搜索完一圈
            return NULL;
        }
    }

    // 分配页面
    int i;
    for (i = 0; i < n; i++) {
        len = list_next(le);
        struct Page *pp = le2page(le, page_link);
        SetPageReserved(pp);
        ClearPageProperty(pp);
        list_del(le);
        le = len;
    }

    // 处理剩余块
    if (p->property > n) {
        struct Page *remaining = le2page(le, page_link);
        remaining->property = p->property - n;
        SetPageProperty(remaining);
        ClearPageReserved(remaining);
    }

    // 更新 rem
    rem = le;
    if (rem == &free_list) {
        rem = list_next(rem);
    }

    nr_free -= n;
    return p;
}
```

- 注意重置空闲块链表时要重置rem指针为链表头！（`basic_check`未实现，测试会不通过）

## 最佳适应分配算法的实现


```c
static struct Page *
best_fit_alloc_pages(size_t n) {
    assert(n>0);
    if(nr_free < n) {
        return NULL;
    }
    // 记录要分配的链表项指针和该块的大小
    list_entry_t *le = &free_list,*to_alloc = NULL,*len;
    size_t minsize = 0x3f3f3f3f;
    while((le = list_next(le)) != &free_list) {
        struct Page* p =le2page(le,page_link);
        // 不够大则跳过
        if(p->property < n) continue;
        // 如果该块比要分配的块大小还要小，则更新要分配的块
        if(p->property < minsize) {
            minsize = p->property;
            to_alloc = le;
        } 
    }
    struct Page * pg = le2page(to_alloc,page_link);
    // 如果块无效，分配失败
    if(to_alloc == NULL || pg->property < n) 
        return NULL;
    // 分配块
    le = to_alloc;
    int i;
    for(i=0;i<n;i++) {
        len = list_next(le);
        struct Page * pp = le2page(le,page_link);
        SetPageReserved(pp);
        ClearPageProperty(pp);
        list_del(le);
        le = len;
    }
    // 剩余空闲块
    if(pg->property>n) {
        le2page(le,page_link)->property = pg->property - n;
    }
    nr_free -= n;
    return pg;
}
```
