# Access instance through console

Trong OPS, có một vài cách để truy cập tới giao diện console của instance như sau:
- **novnc**: VNC client sử dunnjg HTML5 Canvas và WebSockets
- **spice**: A complete in-browser client solution for interaction with virtualized instances
- **xvpvnx**: A Java client offering console access to an instance

Ví dụ truy cập tới instance sử dunnjg `xvpvnc`
```
openstack console url show <INSTANCE_NAME/ID> --xvpvnc/--novnc/--spice
```
hoặc
```
nova get-vnc-console INSTANCE_NAME VNC_TYPE
```

