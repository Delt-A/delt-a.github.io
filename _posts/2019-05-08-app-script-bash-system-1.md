---
layout: post
published: true
title: 'App-Script: Bash — System 1'
---
Mình nghĩ challenge này không cần viết nhưng mà nhận ra nếu không viết ra thì rất dễ quên.

Kiểm tra tại thư mục:
![]({{site.baseurl}}/https://cdn-images-1.medium.com/max/800/1*o5nwjd2WcJqUVj68SBqgig.png)

Đây là code của ch11. Nó sẽ chạy lệnh ls /challenge/app-script/ch11/.passwd


Flag nằm ở /challenge/app-script/ch11/.passwd nhưng ta không có quyền truy cập. Ta sẽ tìm cách biến đổi để cho ls trở thành một lệnh nào khác, đơn giản có thể là cat.

Ở đây ta tạo symbolic link (liên kết động) tên là ls liên kết với file /bin/cat.




Kiểm tra thì chỉ có /tmp là tạo được file và thực thi. Vậy thì tạo symlink ở đó thôi ;)

Câu lệnh tạo symlink: ln -s filename linkname