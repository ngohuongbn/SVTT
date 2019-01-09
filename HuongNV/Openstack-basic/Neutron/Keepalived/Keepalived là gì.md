# Tìm hiểu về Keepalived

# Mục lục
- [1. Keepalive là gì?](#1)
- [2. Keepalived Failure IP hoạt động như thế nào?](#2)
- [3. Configure Keepalived](#3)
    - [3.1 3.1 Global Definitions](#31)
    - [3.2 3.2 VRRP instances](#32)

<a name="1"></a>

# 1. Keepalive là gì?
Keepalive là những gói tin chứa thông điệp được gửi từ một thiết bị(router, switch, vps, cloud server...) đến một thiết bị khác trong mô hình hệ thống mạng HA với mục đích là kiểm tra trạng thái kết nối giữa hai thiết bị có hoạt động không hay thiết bị đầu cuối còn sống không. Gói thông điệp Keepalive thường rất nhỏ và chiếm ít về băng thông, còn nội dung của gói tin keepalive phụ thuộc vào thiết kế của các giao thức hoặc dịch vụ. Có thể coi Keepalive là một hình thức về mặt kĩ thuật chứ không phải là giao thức.

Keepalived là một chương trình dịch vụ trên linux cung cấp khả năng tạo độ sẵn sàng cao cho hệ thống dịch vụ và khả năng cân bằng tải (LB) đơn giản. Với sự gọn nhẹ, tối ưu trong dịch vụ HA của Keepalived mang đến cho quản trị viên một giải pháp Active-Backup dịch vụ rất tốt. Thế nhưng tính năng LB của Keeplived thì không linh hoạt như Nginx hoặc HAProxy. 

![Imgur](https://i.imgur.com/g61wF6Q.png)

Chính vì vậy khi sử dụng Keepalived, người ta hay sử dụng tính năng HA IP Failure của nó chứ ít đả động tới tính năng LB.

Để hoạt động Keepalive ta cần lưu ý 2 điểm chính:
- **keepalive interval**: Thời gian giữa các lần gửi gói tin keepalive từ thiết bị. Gía trị này thường do ta cấu hình.
- **keepalive retries**: Số lần mà từ một thiết bị cố gắng gửi gói tin keepalive kiểm tra trạng thái khi không nhận được phản hồi từ thiết bị khác. Nếu quá số lần này thì có thể xem là thiết bị đầu kia không có trạng thái đường kết nối hoặc là đã đứt, chết.

Một số vấn đề cần đề cập tới Keepalived như:
- Keepalived cung cấp các bộ thư viện cho 2 chức năng chính là cân bằng tải cùng cơ chế health checking và độ sẵn sàng cao cho hệ thống (HA) với VRRP.
- Tính năng cân bằng tải sử dụng Linux Virtual Server(IPVS) module kernel trên Linux.
- Tính năng kiểm tra tình trạng sức khỏe của các máy chủ backend cũng khá linh động giúp duy trì được pool server dịch vụ nào còn sống để cân bằng tải tốt.


Hoạt động của Keepalive sẽ như sau:
- Một gói tin keepalive sẽ được gửi từ một thiết bị A với số thời gian quy định giữa các lần gửi đến thiết bị B
- Sau khi gói tin keepalive đó gửi đi, A sẽ mong đợi phản hồi từ B để kiểm tra đường kết nối giữa hai thiết bị đang hoạt động ổn định.
- Nếu không nhận được phản hồi, A sẽ gửi tiếp một số lần thử gửi lại gói tin (retries) và chờ đợi tiếp
- Nếu sau `n` lần gửi gói tin mà vẫn không nhận được ohanr hồi, thiết bị A sẽ coi B như là đã chết và đường truyền giữa 2 thiết bị lúc này coi như đã `down` và chuyển hướng sang route khác

<a name="2"></a>

# 2. Keepalived Failure IP hoạt động như thế nào?
**Keepalived** sẽ gom nhóm các máy chủ dịch vụ nào tham gia cụm HA, khởi tạo một `virtual server` đại diện cho một nhóm thiết bị đó với một `Virtual IP (VIP)` và một địa chỉ MAC vật lý của máy chủ dịch vụ đang giữ virtual IP đó. Vào mỗi thời điểm nhất định, chỉ có một server dịch vụ dùng địa chỉ MAC này tương tác với VIP. Khi có ARP request gửi tới virtual IP thì server dịch vụ đó sẽ trả về địa chỉ MAC này. 

Các máy chủ dịch vụ sử dụng chung VIP phải liên lạc với nhau bằng địa chỉ `multicast 224.0.0.18` bằng giao thức VRRP. Các máy chủ sẽ có độ ưu tiên (priority) trong khoảng từ 1-254, và máy chủ nào có độ ưu tiên cao nhất sẽ thành `Master`, các máy chủ còn lại sẽ thành `Slave/Backup` hoạt động ở chết độ chờ 

![Imgur](https://i.imgur.com/yJO3Jpa.png)

Nếu vì một sự cố gì đó mà các server BACKUP không nhận được các gói tin quảng bá từ MASTER trong một khoảng thời gian nhất định thì cả nhóm sẽ bầu ra một MASTER mới. MASTER mới này sẽ tiếp quản địa chỉ VIP của nhóm và gửi các gói tin ARP báo nó là đang giữ địa chỉ VIP này. Khi MASTER cũ hoạt động bình thường trở lại thì server này có thể lại trở thành MASTER hoặc trở thành BACKUP tùy theo cấu hình độ ưu tiên. 

<a name="3"></a>

# 3. Configure Keepalived
Thư mục cấu hình keepalived nằm tại đường dẫn
```
/etc/keepalived/keepalived.conf
```

<a name="31"></a>

## 3.1 Global Definitions 

```
global_defs {

   notification_email {
       admin@example.com
   }
   notification_email_from noreply@example.com
   smtp_server 127.0.0.1
   smtp_connect_timeout 60
}
```
- global_defs: block cấu hình global
- notification_email: email nhận thông báo
- notification_email_from: email gửi thông báo
- smtp_server: đia chỉ của SMTP server
- smtp_connect_timeout: cấu hình timeout cho SMTP

<a name="32"></a>

## 3.2 VRRP instances
Dưới đây mô tả file cấu hình cho 2 node MASTER và BACKUP như sau:
### MASTER node
```
vrrp_sync_group VG1 {
   group {
      RH_EXT
      RH_INT
   }
}

vrrp_instance RH_EXT {
    state MASTER
    interface eth0
    virtual_router_id 50
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass passw123
    }
    virtual_ipaddress {
    10.0.0.1
    }
}

vrrp_instance RH_INT {
   state MASTER
   interface eth1
   virtual_router_id 2
   priority 100
   advert_int 1
   authentication {
       auth_type PASS
       auth_pass passw123
   }
   virtual_ipaddress {
       192.168.1.1
   }
}
```
- **vrrp_sync_group**: Định nghĩa 1 VRRP group. Tại group này, khai báo 2 interface là RH_EXT - đi ra internet, RH_INT - interface internal cho mạng private bên trong
- **vrrp_instance**: Block này cấu hình chi tiết cho dịch vụ VRRP như VIP address là bao nhiêu
- **state**: Trạng thái của node là MASTER hay BACKUP
- **interface**: khai báo interface trên node đó
- **virtual_router_id**: khai báo VRRP router ID
- **priority**: Set priority cho node, giá trị từ 0-255. Node nào có priority cao hơn sẽ thành MASTER
- **authentication**: Định nghĩa kiểu xác thực `auth_type` và khai báo mật khẩu xác thực `auth_pass`
- **virtual_ipaddress**: Khai báo VIP. Oử đây ví dụ VIP là `192.168.1.1`
- **advert_int**: Khai báo số advertisement interval trong 1s

### BACKUP node
```
vrrp_sync_group VG1 {
   group {
      RH_EXT
      RH_INT
   }
}

vrrp_instance RH_EXT {
    state BACKUP
    interface eth0
    virtual_router_id 50
    priority 99
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass passw123
    }
    virtual_ipaddress {
    10.0.0.1
    }
}

vrrp_instance RH_INT {
   state BACKUP
   interface eth1
   virtual_router_id 2
   priority 99
   advert_int 1
   authentication {
       auth_type PASS
       auth_pass passw123
   }
   virtual_ipaddress {
       192.168.1.1
   }
}
```





# Tài liệu tham khảo
- https://cuongquach.com/keepalive-la-gi-tim-hieu-ki-thuat-keepalive-trong-thong-ha.html
- https://cuongquach.com/keepalived-la-gi-tim-hieu-dich-vu-keepalived-high-availability.html
- https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/load_balancer_administration/ch-initial-setup-vsa
- http://www.keepalived.org/manpage.html


