# ch3 实验报告

## 功能总结

```
本次实验在原本代码框架的基础上添加一个TaskInfo结构体，同时对TCB进行修改实现目标查询功能。

①在原TaskManager中添加一个sys_call_times数组，记录每个TCB当中不同种类syscall出现的次数

②为TaskManger实现increase_syscall_times和get_syscall_times函数，分别用于增加syscall的次数和返回sys_call_times数组

③对新实现的函数进行封装

④在系统调用sys_task_info当中对ti进行赋值，分别设置TaskStatus，调用get_syscall_times获得syscall_times数组，调用get_time_ms获得运行时间
```



## 简答作业

### Q1

```
sbi:[rustsbi] RustSBI version 0.3.0-alpha.2, adapting to RISC-V SBI v1.0.0

ch2b_bad_address
错误信息：段错误 (核心已转储)	访问了非法地址
ch2b_bad_instructions
非法指令 (核心已转储)	使用了S特权级下才能使用的命令sret
ch2b_bad_register
非法指令 (核心已转储)	访问了S特权级下才能访问的寄存器sstatus
```

### Q2

#### Q2.1

```rust
刚进入__retore时，a0寄存器代表了后续系统调用的第一个参数，用于恢复栈顶指针sp

__restore的使用场景：
从系统调用或者异常返回时，恢复用户态程序的上下文信息
内核态中任务切换时，恢复要切换的任务的上下文信息
```

#### Q2.2

```rust
ld t0, 32*8(sp) // 把之前保存的sstatus寄存器的值取出来保存至t0
ld t1, 33*8(sp) // 把之前保存的sepc寄存器值取出来保存至t1
ld t2, 2*8(sp) // 把之前保存的sscratch值取出来保存至t2
csrw sstatus, t0 // 把t0寄存器的值恢复到sstatus寄存器
csrw sepc, t1 // 把t1寄存器的值恢复到sepc寄存器
csrw sscratch, t2 // 把t2寄存器的值恢复到sscratch寄存器
```

### Q3

```rust
ld x1, 1*8(sp)
ld x3, 3*8(sp)
.set n, 5
.rept 27
   LOAD_GP %n
   .set n, n+1
.endr
sp(x2)寄存器的值我们通过sscratch来保存
在本阶段实现的程序当中，tp(x4)寄存器用不到
```

### Q4

```rust
csrrw sp, sscratch, sp
进行这条指令之后，sp和sscratch中的值互换，sp指向内核栈栈顶，sscratch指向用户栈栈顶
```

### Q5

```rust
状态切换指令：sret
这条指令执行之后程序继续执行sepc保存的地址处的程序，即返回到系统调用指令的下一条指令的地址
```

### Q6

```rust
csrrw sp, sscratch, sp
这条指令结束之后，sp和sscratch互换，sp此时指向内核栈栈顶，sscratch指向用户栈栈顶
```

### Q7

```rust
ecall指令
```



## 荣誉准则

1. 在完成本次实验的过程（含此前学习的过程）中，我曾分别与 **以下各位** 就（与本次实验相关的）以下方面做过交流，还在代码中对应的位置以注释形式记录了具体的交流对象及内容：

   > 无交流对象和交流内容

2. 此外，我也参考了 **以下资料** ，还在代码中对应的位置以注释形式记录了具体的参考来源及内容：

   > *无参考资料*

3. 我独立完成了本次实验除以上方面之外的所有工作，包括代码与文档。 我清楚地知道，从以上方面获得的信息在一定程度上降低了实验难度，可能会影响起评分。

4. 我从未使用过他人的代码，不管是原封不动地复制，还是经过了某些等价转换。 我未曾也不会向他人（含此后各届同学）复制或公开我的实验代码，我有义务妥善保管好它们。 我提交至本实验的评测系统的代码，均无意于破坏或妨碍任何计算机系统的正常运转。 我清楚地知道，以上情况均为本课程纪律所禁止，若违反，对应的实验成绩将按“-100”分计。