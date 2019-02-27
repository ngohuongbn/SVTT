# Reboot an instance

Có thể sử dụng soft reboot hoặc hard reboot để có thể restart lại instance

Mặc định khi reboot sẽ là soft reboot
```
openstack server reboot <SERVER/ID_MAY_AO>
```
Để sử dụng hard reboot, thêm tùy chọn `--hard`
```
openstack server reboot --hard <SERVER/ID_MAY_AO>
```

# Nova-rescue
Trong Nova, OPS cung cấp thêm chức năng `rescue`, chức năng này được hiểu như restore lại instance khi  các instance trong trường hợp hỏng hóc, mất filesystem, mất SSH key, các lỗi trong quá tình thiết lập IPTABLES, quên mật khẩu.

*Chú ý: Pause/Suspend/Stop không thể dùng được trong khi instance đang ở chế độ rescue. Khi sử dụng các tùy chọn trên sẽ gây ra mất mát trạng thái của các instance ban đầu, khiến nó không thể giải quyết được lỗi lầm trước đó.

Để reboot instance trong chế độ rescue, sử dụng:
```
openstack server rescue <SERVER/ID_MAY_AO>
```

*Chú ý: Khi sử dụng `openstack server rescue`, instance sẽ được soft shutdown trước tiên, rồi mới về trạng thái tắt hoàn toàn (powered off). Thời gian shutdown có thể điều chỉnh qua tham số `shutdown_timeout` trong file `nova.conf`, giá trị mặc định là `60s`

- Rescue instance tại một image cụ thể, ví dụ như image ubuntu/centOS
```
openstack rescue -image <IMAGE_ID> <SERVER/ID_MAY_AO>
```

*Chú ý: Khi instance ở rescue mode, disk của instance được đính kèm dưới dạng thứ cấp. Để có thể truy cập được dữ liệu trên disk, phải thực hiện `mount` disk. Thực hiện SSH tới instance, chạy câu lệnh:
```
mount <path to disk> /mnt
```
- Sau khi đã thực hiện rescue instance, tiến hành exit rescue mode và update lại trạng thái cho instance. Lúc này instancw ở trạng thái ACTIVE
```
openstack server unrescue <SERVER/ID_MAY_AO>
```

# Tham khảo
- https://networkforbeginners.com/openstack-rescue-mode/
- https://blog.codybunch.com/2017/09/08/Rescuing-an-OpenStack-instance/
- https://docs.openstack.org/ocata/user-guide/cli-reboot-an-instance.html