---
title: CSAPP arch lab
date: 2020-03-16T21:24:00+08:00
categories: CSAPP
tags: ["搬家"]
---

# arch lab

[Download archlab-handout](https://github.com/Anlarry/CSAPP/blob/master/archlab-handout.tar)

>## 安装模拟器
>
>解决`undefined reference to ’matherr‘`
>
>参考 [Y86-64模拟器的安装与出现对'matherr'未定义引用问题的解决](https://blog.csdn.net/g_x_l_233/article/details/83957381)


## Part A

在这部分要在`sim/misc`中完成，我们要编写和模拟三个`Y86-64`程序

**`sum.ys`： 遍历链表求和**

```
# begin at 0

    .pos 0
    irmovq stack,%rsp
    call main
    halt

    .align 8
    ele1:
        .quad  0x00a
        .quad ele2
    ele2:
        .quad 0x0b0
        .quad ele3
    ele3:
        .quad 0xc00
        .quad 0

main:
    irmovq ele1,%rdi
    call rsum
    ret

rsum:
    xorq %rax,%rax
    andq %rdi,%rdi 
    je L1
    pushq %rbx
    mrmovq (%rdi),%rbx
    mrmovq 8(%rdi),%rdi
    call rsum
    addq %rbx,%rax
    popq %rbx
L1:
    ret

    .pos 0x200
stack:

```

用`YAS`编译后，再用`YIS`运行

    linux> make *.yo
    inux> ../misc/yis *.yo


**`rsum.ys`: 用递归的方式求和**

```
# begin at 0

    .pos 0
    irmovq stack,%rsp
    call main
    halt

    .align 8
    ele1:
        .quad  0x00a
        .quad ele2
    ele2:
        .quad 0x0b0
        .quad ele3
    ele3:
        .quad 0xc00
        .quad 0

main:
    irmovq ele1,%rdi
    call rsum
    ret

rsum:
    xorq %rax,%rax
    andq %rdi,%rdi 
    je L1
    pushq %rbx
    mrmovq (%rdi),%rbx
    mrmovq 8(%rdi),%rdi
    call rsum
    addq %rbx,%rax
    popq %rbx
L1:
    ret

    .pos 0x200
stack:

```

**`copy.ys`: 将src复制到dst**

```
# begin at 0

    .pos 0
    irmovq stack,%rsp
    call main
    halt

    .align 8
    src:
        .quad 0x00a
        .quad 0x0b0
        .quad 0xc00
# Destination block
    dest:
        .quad 0x111
        .quad 0x222
        .quad 0x333

main:
    irmovq src,%rdi
    irmovq dest,%rsi
    irmovq $3,%rdx
    call copy_block
    ret

copy_block:
    xorq %rax, %rax
    irmovq $1,%r14
    irmovq $8,%r13
    pushq %rbx
    andq %rdx,%rdx
    jmp test
loop:
    mrmovq (%rdi),%rbx
    addq %r13,%rdi
    rmmovq %rbx,(%rsi)
    addq %r13,%rsi
    xorq %rbx,%rax
    subq %r14,%rdx
test:
    jne loop
    popq %rbx
    ret

    .pos 0x200
stack:

```

## Part B

这部分在`sim/seq`完成，我们需要修改`seq-full.hcl`， 给`SEQ`添加`iaddq`指令



[seq-full.hcl](https://github.com/Anlarry/CSAPP/blob/master/archlab-handout/sim/seq/seq-full.hcl)

然后根据文档测试

```
./ssim -t ../y86-code/asumi.yo
(cd ../y86-code; make testssim)
(cd ../ptest; make SIM=../seq/ssim)
(cd ../ptest; make SIM=../seq/ssim TFLAGS=-i)
```

测试均成功


## Part C

这部分要在`sim/pipe`中完成

我们的目的是优化` ncopy `，然他更快完成，会根据`CPE`给出一个分数

**Step1**

添加`iaddq`（类似于**PartB**），然后测试

```
unix> make psim VERSION=full
unix> ./psim -t sdriver.yo
unix> ./psim -t ldriver.yo
unix> ./correctness.pl
unix> ./benchmark.pl
```

然而竟然还是`0.0/60.0`，此时CPE大概可以达到`11`左右，可是从`10.5`才开始记分

**Step2**

考虑循环展开，将循环展开8次，分数大概可以提高到`40`多

>循环展开后，就可以改变指令执行的顺序，避免数据冒险
> ```
> mrmovq (%rdi),%r8
> rmmovq %r8,(%rsi)
> ```
> 这样会有一个气泡，如果先把8个数据都放到寄存器，在放到目标地址就可以消除气泡，降低执行的周期数

**Step3**

然而还有剩下的余数没有处理，可以考虑将剩下的余数再循环展开一次，此时已经有50多分了

**Step4**

将循环展开的次数提高到10次，那么性能会提高一点，但不是很明显

优化余数的处理方法，最终可以将分数提高到`56.1`,平均`CPE=7.69`, `ncopy length= 847 bytes`

>应该还可以优化`pipe-full.hcl`，`ncopy.ys`因该也还有提高的空间，以后在补上。。。

[ncopy.ys](https://github.com/Anlarry/CSAPP/blob/master/archlab-handout/sim/pipe/ncopy.ys)
```
ncopy:
	xorq %rax,%rax		# count = 0
	iaddq $-10,%rdx      # 
	jl L2		# if so, goto Done: ## if so, goto Loop
Loop:	
	mrmovq (%rdi), %r8
	mrmovq 0x8(%rdi), %r9
	mrmovq 0x10(%rdi), %r10
	mrmovq 0x18(%rdi), %r11
	mrmovq 0x20(%rdi), %r12
	mrmovq 0x28(%rdi), %r13
	mrmovq 0x30(%rdi), %r14
	mrmovq 0x38(%rdi), %rcx
	mrmovq 0x40(%rdi), %rbx
	mrmovq 0x48(%rdi), %rbp
	andq %r8,%r8
	rmmovq %r8, (%rsi)
	rmmovq %r9, 8(%rsi)
	rmmovq %rbx, 0x40(%rsi)
	rmmovq %rbp, 0x48(%rsi)
	jle L1_2
	iaddq $1, %rax
L1_2:
	andq %r9,%r9
	jle L1_3
	iaddq $1, %rax
L1_3:
	andq %r10,%r10
	rmmovq %r10, 0x10(%rsi)
	rmmovq %r11, 0x18(%rsi)
	jle L1_4
	iaddq $1, %rax
L1_4:
	andq %r11,%r11
	jle L1_5
	iaddq $1, %rax
L1_5:
	andq %r12,%r12
	rmmovq %r12, 0x20(%rsi)
	rmmovq %r13, 0x28(%rsi)
	jle L1_6
	iaddq $1, %rax
L1_6:
	andq %r13,%r13
	jle L1_7
	iaddq $1, %rax
L1_7:
	andq %r14,%r14
	rmmovq %r14, 0x30(%rsi)
	rmmovq %rcx, 0x38(%rsi)
	jle L1_8
	iaddq $1, %rax
L1_8:
	andq %rcx,%rcx
	jle L1_9
	iaddq $1, %rax
L1_9:
	andq %rbx, %rbx
	jle L1_10
	iaddq $1, %rax
L1_10:
	andq %rbp,  %rbp
	jle L1_11
	iaddq $1, %rax
L1_11:
	iaddq $0x50, %rdi
	iaddq $0x50, %rsi
	iaddq $-10, %rdx
	jge Loop
L2:
	iaddq $9, %rdx
	jg Loop2
	jl L2_Done
	mrmovq (%rdi),%r8
	rmmovq %r8, (%rsi)
	andq %r8, %r8
	jle Done
	iaddq $1, %rax
L2_Done:
	ret
	
Loop2:
	mrmovq (%rdi), %r8
	mrmovq 8(%rdi), %r9
	iaddq $-2, %rdx
	jg L2_0
	je D3 # last 3
	rmmovq %r8, (%rsi)
	rmmovq %r9, 8(%rsi)
	andq %r8,%r8
	jle D2_1
	iaddq $1, %rax
D2_1:
	andq %r9, %r9
	jle Done
	iaddq $1, %rax
	ret
D3:
	mrmovq 0x10(%rdi), %r10
	rmmovq %r8, (%rsi)
	rmmovq %r9, 8(%rsi)
	rmmovq %r10, 0x10(%rsi)
	andq %r8, %r8
	jle D3_1
	iaddq $1, %rax
D3_1:
	andq %r9, %r9
	jle D3_2
	iaddq $1, %rax
D3_2:
	andq %r10, %r10
	jle Done
	iaddq $1, %rax
	ret

L2_0:
	rmmovq %r8, (%rsi)
	rmmovq %r9, 8(%rsi)
	andq %r8,%r8
	jle L2_1
	iaddq $1, %rax
L2_1:
	andq %r9, %r9
	jle L2_2
	iaddq $1, %rax
L2_2:
	iaddq $0x10, %rdi
	iaddq $0x10, %rsi
	jmp Loop2
Done:
	ret
End:

```


**Step5 待更**

。。。
