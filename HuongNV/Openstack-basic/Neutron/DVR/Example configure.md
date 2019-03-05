# Ví dụ về cấu hình mẫu

# Trên controller node
Chỉnh sửa file `/etc/neutron/neutron.conf`
```
[DEFAULT]
verbose = True
router_distributed = True
core_plugin = ml2
service_plugins = router
allow_overlapping_ips = True
```

```
router_distributed =  True  cho phép tất cả users có thể tạo distributed routers. Nếu không khai báo tùy chọn này, chỉ admin mới có thể tạo DVR với tham số --distributed True
```
