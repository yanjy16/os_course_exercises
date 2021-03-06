# lec10: lab3 SPOC思考题

## 视频相关思考题
---
### 10.1 实验目标：虚存管理
---

1. 缺页和页访问非法的返回地址有什么不同？
> 硬件设置，软件可修改

2. 虚拟内存管理中是否用到了段机制？
> 用到了段机制

3. ucore如何知道页访问异常的地址？
> CR2寄存器中保存页访问异常的地址

### 10.2 回顾历史和了解当下
---

1. 中断处理例程的段表在GDT还是LDT？
> 中断处理例程的段表存在GDT

2. 物理内存管理的数据结构在哪？
> 在Page中

3. 页表项的结构？
> 高20位保存对应物理页页帧的起始地址，低12位为标志位

4. 页表项的修改代码？
> 缺页异常处理代码
 
5. 如何设置一个虚拟地址到物理地址的映射关系？
> 修改页表项，从而实现映射关系
 
6. 为了建立虚拟内存管理，需要在哪个数据结构中表示“合法”虚拟内存
> 设置页表项的有效位
 
### 10.3 处理流程、关键数据结构和功能
---

1. swap_init()做了些什么？
> 初始化对换分区，以页为单位的对硬盘的读写

2. vmm_init()做了些什么？
> 初始化逻辑地址空间

3. vma_struct数据结构的功能？
> 描述和管理合法的内存块的空间

4. mmap_list是什么列表？
> 用于维护连续逻辑地址空间映射的列表

5. 外存中的页面后备如何找到？
> swap_in_page

6. vma_struct和mm_struct的关系是什么？
> vma_struct表示合法的连续虚拟地址区域，而mm_struct指整个进程的地址空间

7. 画数据结构图，描述进程的虚拟地址空间、页表项、物理页面和后备页面的关系；

### 10.4 页访问异常
---

1. 页面不在内存和页面访问非法的处理中有什么区别？对应的代码区别在哪？
> pgfault_handler中的处理不同，合法地址应有效应对，即使不在内存；非法地址需要报错

2. find_vma()做了些什么？
> 看逻辑地址是否合法，看产生异常的地址是否属于某个vma
 
3. swapfs_read()做了些什么？
> 将磁盘中的页面数据读到内存中
 
4. 缺页时的页面创建代码在哪？
> page_insert
 
5. struct rb_tree数据结构的原理是什么？在虚拟管理中如何用它的？
> 红黑树，加快查找速度
 
6. 页目录项和页表项的dirty bit是何时，由谁置1的？
> 在页面被修改时，硬件将dirty bit置1
 
7. 页目录项和页表项的access bit是何时，由谁置1的？
> 在页面被访问时，硬件将dirty bit置1

### 10.5 页换入换出机制
---

1. 虚拟页与磁盘后备页面的对应有关系？
> PTE中的存在位为0时，swap_entry_t表示的是虚拟页与磁盘扇区的对应关系
 
2. 如果在开始加载可执行文件时，如何改？
> ？
 
3. check_swap()做了些什么检查？
> 检验页替换算法能否正确完成  
检查了虚拟页面和物理页面之间的映射、页面换入换出机制、FIFO页替换算法的正确性。
 
4. swap_entry_t数据结构做什么用的？放在什么地方？
> 表明虚拟页和磁盘扇区的映射关系  
放在PTE中
 
5. 空闲物理页面的组织数据结构是什么？
> free_list
 
6. 置换算法的接口数据结构？
> swap_maneger

================


## 小组思考题
---
(1)请参考lab3_result的代码，思考如何在lab3_results中实现clock算法，给出你的概要设计方案。可4人一个小组。要求说明你的方案中clock算法与LRU算法上相比，潜在的性能差异性。进而说明在lab3的LRU算法实现的可能性评价（给出理由）。

(2) 理解内存访问的异常。在x86中内存访问会受到段机制和页机制的两层保护，请基于lab3_results的代码（包括lab1的challenge练习实现），请实践并分析出段机制和页机制各种内存非法访问的后果。，可4人一个小组，，找出尽可能多的各种内存访问异常，并在代码中给出实现和测试用例，在执行了测试用例后，ucore能够显示出是出现了哪种异常和尽量详细的错误信息。请在说明文档中指出：某种内存访问异常的原因，硬件的处理过程，以及OS如何处理，是否可以利用做其他有用的事情（比如提供比物理空间更大的虚拟空间）？哪些段异常是否可取消，并用页异常取代？

## 课堂实践练习

请分析ucore中与物理内存管理和虚拟存储管理相关的数据结构组织；分析访问这些数据结构的函数，说明其对存储管理相关数据结构的修改情况；最后通过一个数据结构图示，描述进程的虚拟地址空间、页表项、物理页面和后备页面的关系。

 * struct Page
 * struct mm_struct
 * struct vma_struct
 * struct swap_entry

## v9-cpu相关
---
(1)分析并编译运行v9-cpu git repo的testing branch中的,root/etc/os_lab2.c os_lab3.c os_lab3_1.c,理解虚存机制是如何在v9-cpu上实现的，思考如何实现clock页替换算法，并给出你的概要设计方案。

(2)分析并编译运行v9-cpu git repo的testing branch中的,root/etc/os_lab2.c os_lab3.c os_lab3_1.c，理解内存访问异常的各种情况，并给出你的分析结果。
