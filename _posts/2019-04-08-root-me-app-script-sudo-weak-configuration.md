---
layout: post
published: true
title: "root-me-App-Script"
date: '2018-07-25'
subtitle: 'App-Script: sudo — weak configuration'
---
Đây là self-note để ghi nhớ sau này mình có chỗ còn coi lại. Phần này thuộc challenge của root-me.org

Khi truy cập ssh vào thì ta kiểm tra tại thư mục đó có gì:
![](https://cdn-images-1.medium.com/max/800/1*Kml88Ux85BzDq6E-b1FHYQ.png)

Okay, bây giờ ta kiểm tra file readme.md trước:

![](https://cdn-images-1.medium.com/max/800/1*zCs-v3krrSbg33IYSm3cgg.png)  
Nó cho ta biết được cần phải tìm flag ở đâu.

Truy cập vào /ch1cracked để kiểm tra thử xem sao. Bài này liên quan tới sudo nên ta check sudo -l: (Aliase -tạm dịch là “ bí danh”)

![](https://cdn-images-1.medium.com/max/800/1*mibZwY9RqhCxAKfLwuQ_HA.png)
sudo -l (trong tài liệu của root-me cung cấp)
Khi được yêu cầu nhập mật khẩu, ta nhập mật khẩu của user, không phải root password.


Từ kết quả, user app-script-ch1 có thể dùng những câu lệnh trên challenge02:

(app-script-ch1-cracked) /bin/cat /challenge/app-script/ch1/ch1/*

Theo mình hiểu được thì có thể chạy dưới “bí danh” app-script-ch1-cracked, thực hiện lệnh cat đối với đường dẫn /challenge/app-script/ch1/ch1/* (* nghĩa là tất cả trường hợp)


sudo -u
Theo những gì hiểu được và 1 hồi mày mò thì đã ra được đáp án đúng:

sudo -u app-script-ch1-cracked cat /challenge/app-script/ch1/ch1/*/../ch1cracked/.passwd

Hoặc có thể dùng câu lệnh khác, ví dụ như:

sudo -u app-script-ch1-cracked cat /challenge/app-script/ch1/ch1/shared_notes /challenge/app-script/ch1/ch1cracked/.passwd

Lợi dụng việc cat có thể mở một lúc nhiều file, ở câu lệnh trên ta mở file:

/challenge/app-script/ch1/ch1/shared_notes
Đây là đường dẫn giống như là “khử” điều kiện của /challenge/app-script/ch1/ch1/* ((app-script-ch1-cracked) /bin/cat /challenge/app-script/ch1/ch1/*), và đường dẫn sau chính là đường dẫn mình cần phải mở sau khi điều kiện sudo đã thỏa.
