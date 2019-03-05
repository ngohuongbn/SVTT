# Tìm hiểu về DVR trong Neutron

# Mục lục
- [1. Arrchitecture](#1)
    - [1.1 Network node](#11)
    - [1.2 Compute node](#2)

DVR Distributed Virtual Routing cho phép ta deploy router trên mỗi con Compute node. Traffic giữa các VM tại các node Eất-West sẽ được định tuyến tại các router ảo này mà không cần thông qua router tại Network node. Ngoài ra, `Floating IP ` namespace được attach tới từng con VM, trafic từ các VM này sẽ được gửi trực tiếp ra mạng ngoài North-South mà không cần thông qua Network node.

Với DVR, compute node cung cấp thêm các lợi ích như sau:
- Nâng cao hiệu suất, vì không cần thông qua network node
- Khả năng mở rộng được cải thiện
- Việc failure domain trên mỗi compute node được giảm đi
- 

Sơ đồ dưới đây miêu tả 2 VM nằm trên 2 node khác nhau. 2 VM này nằm trên 2 subnet khác nhau có thể ping thông tới nhau mà không cần qua Network node.

![Imgur](https://i.imgur.com/26yTmI8.png)

<a name="1"></a>

# 1. Architecture

<a name="11"></a>

## 1.1 Network node
Network node bao gầm các thành phần như sau:
1. OVS quản lý các switch ảo, quản lý việc kết nối giữa chúng
2. DHCP agent, `qdhcp namespace`, nó có nhiệm vụ cung cấp DHCP tới các instance
3. L3 agent quản lý `qrouter` và `snat` namespace
- qrouter sẽ thưc hiện việc định tuyến giữa các instance `north-south` và `east-west`, thực hiện DNAT/SNAT. Ngoài ra, qrouter còn thực hiện định tuyến các metadata traffic giữa instance và metadata agent.
- Các instance đính kèm với mỗi fixed IP address, `snat` namespace sẽ thực hiện SNAT các traffic NORTH-SOUTH
4. Meatadata agent cho các VM

![Imgur](https://i.imgur.com/S3vybRP.png)

![Imgur](https://i.imgur.com/gpJ0gQj.png)

