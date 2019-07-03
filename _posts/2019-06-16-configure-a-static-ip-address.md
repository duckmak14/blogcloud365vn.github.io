---
date: 2019-06-16
title: Cài đặt IP static
categories:
  - LINUX TUTORIAL
description:  Hướng dẫn cấu hình ip tĩnh trong centos 7
author: anhduc
tags: [Network,LINUX]
type: Document
---

## Lời mở đầu
Khi sử dụng máy tính vào mạng chúng ta thường chỉ cần cắm dây mạng hoặc bắt wifi là có thể sử dụng được. Là bởi vì khi chúng ta không cài đặt thì sẽ được cấp cho một địa chỉ IP một cách tự động bằng giao thức DHCP để sử dụng. Nhưng nếu khi vấn đề cấp IP tự động này bị lỗi do trùng lặp IP thì chúng ta sẽ sử dụng một cách đó là đặt địa chỉ IP tĩnh. Ở bài này tôi sẽ hướng dẫn các cài đặt IP tĩnh trong linux

## Tổng quan 
Mỗi một thiết bị muốn truy cập được mạng thì sẽ phải có địa chỉ IP. Trong centos 7 địa chỉ IP sẽ được lưu trữ trong một file có tên là `/etc/sysconfig/network-scripts/ifcfg-eth0`. Chúng ta muốn thay đổi địa chỉ thì chúng ta sẽ phải thay đổi thông tin của file này.

Lợi ích của việc sử dụng IP tĩnh :
- Kết nối mạng nhanh hơn vì không phải cấp lại địa chỉ IP 
- IP tĩnh cố định không thay đổi cho dù khởi động lại hệ thống nên có khả năng đáp ứng nhu cầu về game hay dịch vụ cấp cao một cách hoàn hảo nhất
-  IP tĩnh còn hỗ trợ cải thiện tốc độ truy cập intrnet cũng như phục vụ hoạt động có hiệu quả tại các doanh nghiệp, công ty kết nối máy in, sử dụng thiết bị camera IP giám sát từ xa.

## Chuẩn bị
Để có thể làm được bài này chúng ta sẽ cùng nhau chuẩn bị một VM: 
- `OS = centos7`
- RAM = 512MB
- CPU = 1 core
- disk = 10G
- IP(DHCP) : 192.168.122.15

*Lưu ý khi sử dụng giao thức DHCP sẽ được cấp cả địa chỉ IPv6 nhưng ở bài này chúng ta chỉ quan tâm tới IPv4 của nó*  

## Thực hiện 
Sau khi cài đặt xong VM chúng ta cùng vào và kiểm tra địa chỉ IP của máy ban đầu.

![](/images/img-network/ip-static/screenshot_4.png)

Như chúng ta biết thì thông tin địa chỉ IP của card `eth0` được lưu trong file `/etc/sysconfig/network-scripts/ifcfg-eth0` Muốn thay đổi được địa chỉ  IP thì chúng ta sẽ phải vào đây để thay đổi thông tin của nó.
```
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="dhcp"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="eth0"
UUID="d33b005c-975d-4721-bd15-a8b8d474cf6f"
DEVICE="eth0"
ONBOOT="yes"
```

Chúng ta thấy rằng ban đầu máy sẽ sử dụng giao thức DHCP để nhận địa chỉ IP nếu  muốn truy cập mạng. Bây giờ chúng ta sẽ thay đổi địa chỉ IP của nó bằng cách thay đổi các thông số đó như dưới đây
```
TYPE="Ethernet"
BOOTPROTO=static
DEFROUTE="yes"
NAME=eth0
IPADDR=192.168.122.16
NETMASK=255.255.255.0
GATEWAY=192.168.122.1
UUID=d33b005c-975d-4721-bd15-a8b8d474cf6f
DEVICE=eth0
ONBOOT=yes
```
Sau khi chỉnh sửa xong các file chúng ta lưu lại. Sửa file xong thì sẽ chưa thay đổi được địa chỉ IP chúng ta phải khởi động lại dịch vụ mạng với câu lệnh sau
```
systemctl restart network
```

Sau khi khởi động lại dịch vụ mạng chúng ta kiểm tra địa chỉ IP lại một lần nữa 

![](/images/img-network/ip-static/screenshot_1.png)

Chúng ta đã thấy địa chỉ IP của máy đã đổi. Nếu muốn kiểm tra sau khi địa chỉ IP thay đổi có hoạt động bình thường hay không chúng ta sẽ sử dụng lệnh ping để xem máy có thể đi ra được ngoài internet hay không 
```
[root@localhost ~]# ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=53 time=60.1 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=53 time=104 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=53 time=58.2 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=53 time=49.2 ms
64 bytes from 8.8.8.8: icmp_seq=5 ttl=53 time=52.1 ms
^C
--- 8.8.8.8 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4006ms
rtt min/avg/max/mdev = 49.212/64.836/104.418/20.190 ms
```

Kết quả là máy tính vẫn đi ra ngoài được internet bình thường 

**Kết luận** : Ở bài này mình đã hướng dẫn mõi người làm thế nào để đặt địa chỉ IP tĩnh cho máy tính để phòng trường hợp khi DHCP bị lỗi.

Chúc các bạn thành công!

Thực hiện bởi [cloud365](https://cloud365.vn/)

Written by [Nguyễn Anh Đức](https://nhanhoa.com/)

