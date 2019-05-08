---
layout: post
published: true
title: 'App-Script: Bash — System 1'
---
Mình nghĩ challenge này không cần viết nhưng mà nhận ra nếu không viết ra thì rất dễ quên.

Kiểm tra tại thư mục:
![](https://cdn-images-1.medium.com/max/800/1*o5nwjd2WcJqUVj68SBqgig.png)

Đây là code của ch11. Nó sẽ chạy lệnh ls /challenge/app-script/ch11/.passwd


Flag nằm ở /challenge/app-script/ch11/.passwd nhưng ta không có quyền truy cập. Ta sẽ tìm cách biến đổi để cho ls trở thành một lệnh nào khác, đơn giản có thể là cat.

Ở đây ta tạo symbolic link (liên kết động) tên là ls liên kết với file /bin/cat.




Kiểm tra thì chỉ có /tmp là tạo được file và thực thi. Vậy thì tạo symlink ở đó thôi ;)

Câu lệnh tạo symlink: ln -s filename linkname

Add directory vào **$PATH** có thể bằng cách sau:
**PATH=/tmp/abc:$PATH

Với /tmp/abc là địa chỉ của thư mục muốn add vào. Ở trên ta có ví dụ là /tmp/delta
Kiểm tra thì ta đã thành công.

(Nếu bạn không hiểu add vào enviroment variable **$PATH** làm gì thì đừng lo. Ngay bây giờ mình sẽ giải thích. Khi mà bạn truyền vào tên 1 chương trình trên terminal thì terminal sẽ tự động chạy chương trình đó lên, bạn không cần quan tâm chương trình đó ở đâu để chạy đúng không nào? :D

Ví dụ khi bạn gõ python trong terminal thì nó sẽ tự động chạy ngay python ở terminal luôn. Đó là vì nó sẽ kiểm tra tuần tự từ trái sang phải các địa chỉ thư mục nằm trong biến **$PATH**, khi tìm được ở chương trình nào đó đầu tiên thì nó sẽ tự động chạy ngay lên thôi. Lúc bạn add /tmp/abc vào **$PATH** khi chạy lệnh ls nó sẽ tìm ở đây trước, khi đó ta có symlink tên là ls liên kết tới /bin/cat và chạy chương trình cat, và …bùm, flag xuất hiện.)