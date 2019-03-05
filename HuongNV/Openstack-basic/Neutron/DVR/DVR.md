# Tìm hiểu về DVR trong Neutron

DVR Distributed Virtual Routing cho phép ta deploy router trên mỗi con Compute node. Traffic giữa các VM tại các node Eất-West sẽ được định tuyến tại các router ảo này mà không cần thông qua router tại Network node. Ngoài ra, `Floating IP ` namespace được attach tới từng con VM, trafic từ các VM này sẽ được gửi trực tiếp ra mạng ngoài North-South mà không cần thông qua Network node.

Sơ đồ dưới đây miêu tả 2 VM nằm trên 2 node khác nhau. 2 VM này nằm trên 2 subnet khác nhau có thể ping thông tới nhau mà không cần qua Network node.

![Imgur](https://i.imgur.com/26yTmI8.png)
