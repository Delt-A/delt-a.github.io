---
layout: post
published: true
title: 'Pwnable.kr: collision'
subtitle: 'collision - 3 pt [writeup]'
image: '{{site.baseurl}}/img/ctf/pwnable.tw/Capture.PNG'
---
>Daddy told me about cool MD5 hash collision today.
I wanna do something like that too!
>ssh col@pwnable.kr -p2222 (pw:guest)

Through times and times debugging, we will find that check_password() function is a hash algorithm. 

{% highlight c linenos %}
#include <stdio.h>
#include <string.h>
unsigned long hashcode = 0x21DD09EC;
unsigned long check_password(const char* p){
	int* ip = (int*)p;  //gán địa chỉ ip = p;
	int i;
	int res=0;
	for(i=0; i<5; i++){           //lặp 5 lần
		res += ip[i];   //res sẽ là địa chỉ 0x21DD09EC nhưng là tổng của 5 lần cộng
	}
	return res;
}

int main(int argc, char* argv[]){
	if(argc<2){
		printf("usage : %s [passcode]\n", argv[0]); //check có nhập vào hay không 
		return 0;
	}
	if(strlen(argv[1]) != 20){
		printf("passcode length should be 20 bytes\n"); //leng nhập vào = 20 ()
		return 0;
	}

	if(hashcode == check_password( argv[1] )){ //
		system("/bin/cat flag");    
		return 0;
	}
	else
		printf("wrong passcode.\n");
	return 0;
}
{% endhighlight %}


{: .box-note }
./col "`python -c "print '\xc8\xce\xc5\x06'*4 + '\xcc\xce\xc5\x06'"`"

{% highlight python lineos %}

{% endhighlight %}
