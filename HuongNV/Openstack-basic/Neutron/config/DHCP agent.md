# Tìm hiểu về DHCP agent


DHCP agent chịu trách nhiệm phân bổ địa chỉ IP cho các áy ảo chạy trên mạng. Nếu agent được enable và đang chạy, một subnet được khởi tạo thì mặc định DHCP cũng được enable.

## Cài đặt DHCP agent

```
# apt-get install neutron-dhcp-agent
```

## Configure DHCP agent with OVS plug-in

Truy cập file `/etc/neutron/dhcp_agent.ini`

```
[DEFAULT]
enable_isolated_metadata = True
interface_driver = openvswitch   # Cấu hình sử dụng OVS để kết nối các VM
```



