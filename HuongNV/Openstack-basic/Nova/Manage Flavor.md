# Manage Flavor

Trong OPS, flavors được coi như template cho instance. Nó định nghĩa các thông số cho instance như memory, RAM, vCPU, dung lượng lưu trữ cho instance. Mối 1 flavor gồm các thống số như sau:
- **Flavor ID**: ID của flavor
- *8Name**: Tên của flavor
- **vCPU**: Số virtual CPU gán cho mỗi instance
- **Memory MB**:Dung lượng RAM gán cho instance, đơn vị tính bằng MB
- **Root Disk GB**: Dung lượng dùng cho phân vùng `/root`
- **Ephemeral Disk GB**: Kích thước của ephemeral data disk số 2. Đây là đĩa trống, chưa được format và chỉ tồn tại khi máy ảo chạy
- **Swap**: Mặc định là 0
- **RXTX Factor**: Mặc định là 1.0 
- **Is Public**: Cho phép flavor có được public tới toàn bộ người dùng hay không hay là giới hạn truy cập tới một project nào đó. Mặc định flavor được `public`
- **Extra Specs**: Chỉ định xem flavor sẽ nằm trên node compute nào đó cụ thể

# Create flavor
- Sử dụng tùy chọn `openstack help flavor create` để xem thêm các thông số khi khởi tạo 1 flavor
- Liệt kê các flavor hiện có
```
openstack flavor list
```
- Create 1 flavor mới với ID, RAM size
```
openstack flavor create FLAVOR_NAME --id FLAVOR_ID \
    --ram RAM_IN_MB --disk ROOT_DISK_IN_GB --vcpus NUMBER_OF_VCPUS
```
## Tạo  flavor và giới hạn truy cập tới flavor 
- Tạo flavor
```
openstack flavor create --private m1.extra_tiny --id auto \
    --ram 256 --disk 0 --vcpus 1
```
- Gán flavor tới project
```
openstack flavor set --project PROJECT_ID m1.extra_tiny
```
- Có thể set hoặc unset thêm các thống số cho flavor sử dụng câu lệnh
```
openstack flavor set --help
```
# Modify flavor
```
nova flavoe-update FLAVR DESCRIPTION
```
# Xóa flavor
```
openstack flavor delete FLAVOR
```
