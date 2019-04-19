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