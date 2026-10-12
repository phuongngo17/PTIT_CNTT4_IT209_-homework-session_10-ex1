# BÁO CÁO KỸ THUẬT: CONTAINER VS VIRTUAL MACHINE

**Môn học:** DevOps Fundamentals  
**Session:** 10  
**Bài tập:** Exercise 1

## 1. Giới thiệu

Trong quá trình phát triển và triển khai phần mềm, doanh nghiệp thường sử dụng Virtual Private Server (VPS) hoặc Virtual Machine (VM) để vận hành ứng dụng. Tuy nhiên, khi hệ thống phát triển với nhiều dịch vụ, việc quản lý và triển khai trực tiếp trên các máy chủ ảo có thể gặp khó khăn về tài nguyên, tính nhất quán và khả năng mở rộng.

Docker là nền tảng container hóa giúp đóng gói ứng dụng cùng các thư viện và thành phần phụ thuộc vào một môi trường độc lập gọi là Container.

Điểm khác biệt quan trọng là VM thực hiện ảo hóa phần cứng thông qua Hypervisor, trong khi Container thực hiện ảo hóa ở cấp hệ điều hành và chia sẻ Kernel của hệ điều hành chủ.

## 2. So sánh Virtual Machine và Container

| Tiêu chí | Virtual Machine (VM) | Container (Docker) |
|---|---|---|
| Dung lượng Image | Thường lớn, từ vài GB đến hàng chục GB | Thường nhỏ hơn, từ vài MB đến vài GB |
| Thời gian khởi động | Thường mất hàng chục giây đến vài phút | Thường chỉ mất vài giây hoặc ít hơn |
| Hao phí OS | Mỗi VM cần hệ điều hành riêng | Chia sẻ Kernel, không cần Guest OS đầy đủ |
| Mức độ cô lập | Cao hơn nhờ ảo hóa phần cứng và Kernel riêng | Cô lập ở cấp hệ điều hành, chia sẻ Kernel |
| Khả năng mở rộng | Tốn nhiều tài nguyên hơn khi tạo VM mới | Dễ tạo, nhân bản và triển khai nhiều Container |

### 2.1. Kiến trúc Virtual Machine

Virtual Machine sử dụng Hypervisor như VMware, KVM hoặc Hyper-V để tạo ra các máy ảo độc lập.

Mỗi máy ảo bao gồm:
- Ứng dụng (Application).
- Thư viện và thành phần phụ thuộc.
- Hệ điều hành khách (Guest OS).
- Tài nguyên phần cứng ảo.

Ưu điểm của VM là khả năng cô lập mạnh và có thể chạy các hệ điều hành khác nhau trên cùng một máy chủ vật lý.

Nhược điểm là tiêu tốn nhiều RAM, CPU và dung lượng lưu trữ do mỗi VM cần vận hành hệ điều hành riêng.

### 2.2. Kiến trúc Container

Docker sử dụng Container Engine để quản lý các Container.

Mỗi Container bao gồm:
- Ứng dụng.
- Thư viện cần thiết.
- Các thành phần phụ thuộc của ứng dụng.

Các Container chia sẻ Kernel của hệ điều hành chủ thay vì khởi động một hệ điều hành đầy đủ cho từng ứng dụng.

Nhờ vậy, Container thường nhẹ hơn, khởi động nhanh hơn và phù hợp với các hệ thống Microservices.

## 3. Cơ chế Namespaces trong Linux

Namespaces là tính năng của Linux Kernel giúp cô lập môi trường hoạt động giữa các Container.

### 3.1. PID Namespace (Process Isolation)

PID Namespace cô lập không gian định danh tiến trình.

Mỗi Container có thể quan sát và quản lý các tiến trình thuộc không gian PID của mình mà không nhìn thấy toàn bộ tiến trình trên máy chủ.

Ví dụ: Một tiến trình bên trong Container có thể có PID bằng 1, trong khi trên máy chủ tiến trình đó có PID khác.

### 3.2. Network Namespace

Network Namespace cô lập tài nguyên mạng giữa các Container.

Mỗi Container có thể có:
- Địa chỉ IP riêng.
- Network Interface riêng.
- Bảng định tuyến riêng.
- Cấu hình mạng riêng.

Docker có thể sử dụng mạng Bridge để các Container giao tiếp với nhau và kết nối ra bên ngoài.

### 3.3. Mount Namespace

Mount Namespace cô lập các điểm gắn kết hệ thống tệp.

Mỗi Container có thể có góc nhìn riêng về cấu trúc thư mục và các filesystem được mount.

Điều này giúp ứng dụng trong Container hoạt động trong môi trường filesystem riêng, hạn chế ảnh hưởng trực tiếp đến hệ thống tệp của máy chủ.

## 4. Control Groups (cgroups)

Control Groups là cơ chế của Linux Kernel giúp quản lý, theo dõi và giới hạn tài nguyên hệ thống mà các nhóm tiến trình được phép sử dụng.

### 4.1. Giới hạn CPU

Docker cho phép kiểm soát mức sử dụng CPU của Container.

Ví dụ:

```bash
docker run --cpus="1.5" nginx
```

Lệnh trên tạo Container Nginx với giới hạn CPU tương đương 1.5 CPU.

### 4.2. Giới hạn RAM

Docker có thể giới hạn bộ nhớ tối đa của Container.

Ví dụ:

```bash
docker run --memory="512m" nginx
```

Lệnh trên tạo Container Nginx với giới hạn bộ nhớ 512 MB.

### 4.3. Vai trò của cgroups

cgroups giúp:
- Ngăn Container sử dụng tài nguyên vượt mức được cấp.
- Hạn chế ảnh hưởng đến các ứng dụng khác.
- Phân phối tài nguyên hiệu quả.
- Cải thiện tính ổn định của hệ thống.

Namespaces và cgroups kết hợp với nhau để tạo ra môi trường Container có khả năng cô lập và quản lý tài nguyên.

## 5. Phân tích kết quả docker info

### 5.1. Kiểm tra Docker Engine

Sử dụng các lệnh sau trong Windows PowerShell:

```powershell
docker --version
docker version
docker info
```

Các lệnh đều thực thi thành công mà không cần sử dụng quyền `sudo`.

### 5.2. Thông số hệ thống thực tế

| Thông số | Kết quả |
|---|---|
| Server Version | 29.5.2 |
| Cgroup Driver | cgroupfs |
| Cgroup Version | 2 |
| Containers | 34 |
| Running Containers | 0 |
| Stopped Containers | 34 |
| Images | 12 |
| Operating System | Docker Desktop |
| Kernel Version | 6.6.114.1-microsoft-standard-WSL2 |
| Architecture | x86_64 |
| CPUs | 16 |
| Total Memory | 7.594 GiB |

### 5.3. Phân tích kết quả

**Server Version 29.5.2:** Đây là phiên bản Docker Engine đang hoạt động trên hệ thống, chịu trách nhiệm quản lý Docker Images, Containers và các tài nguyên liên quan.

**Cgroup Driver cgroupfs:** Docker sử dụng cgroupfs để tương tác với cơ chế Control Groups của Linux Kernel, giúp quản lý và giới hạn tài nguyên như CPU và RAM.

**Cgroup Version 2:** Hệ thống sử dụng cgroups v2, cung cấp mô hình quản lý tài nguyên thống nhất trong Linux.

**Containers 34:** Docker hiện quản lý tổng cộng 34 Container. Trong đó có 0 Container đang chạy và 34 Container đã dừng.

**Images 12:** Hệ thống hiện lưu trữ 12 Docker Images. Các Image này có thể được sử dụng để tạo Container.

**Môi trường WSL2:** Docker Desktop chạy Linux Container thông qua môi trường Linux trên WSL2 của Windows. Điều này cho phép sử dụng các tính năng Linux Kernel như Namespaces và cgroups.

### 5.4. Kết luận

Kết quả kiểm tra cho thấy Docker Engine hoạt động bình thường, hỗ trợ cgroups v2 và sử dụng cgroupfs làm Cgroup Driver. Hệ thống đã có các Docker Images và Containers phục vụ quá trình học tập, thực hành triển khai ứng dụng.

Các thông số trên được thu thập trực tiếp từ lệnh `docker info`, phản ánh trạng thái Docker tại thời điểm thực hiện bài tập.

## 6. Lý do doanh nghiệp chuyển từ VPS truyền thống sang Docker

### 6.1. Tối ưu tài nguyên

Docker cho phép nhiều ứng dụng sử dụng chung Kernel, giảm nhu cầu vận hành nhiều hệ điều hành độc lập.

### 6.2. Triển khai nhanh chóng

Ứng dụng được đóng gói thành Docker Image và có thể triển khai nhất quán trên nhiều môi trường.

### 6.3. Dễ dàng mở rộng

Doanh nghiệp có thể tạo thêm Container khi nhu cầu xử lý tăng lên. Khi kết hợp với các nền tảng điều phối như Kubernetes, việc mở rộng và quản lý nhiều Container trở nên thuận tiện hơn.

### 6.4. Phù hợp kiến trúc Microservices

Mỗi Microservice có thể được đóng gói trong Container riêng, giúp quản lý phiên bản, cập nhật và triển khai độc lập.

### 6.5. Hỗ trợ CI/CD

Docker giúp chuẩn hóa môi trường build, test và deploy, hạn chế sự khác biệt giữa máy phát triển và môi trường production.

Tuy nhiên, Docker không thay thế hoàn toàn VPS. Doanh nghiệp thường triển khai Docker trên VPS, VM hoặc máy chủ vật lý để kết hợp ưu điểm của cả hai công nghệ.

## 7. Kết luận

Virtual Machine và Container đều đóng vai trò quan trọng trong hạ tầng công nghệ thông tin.

VM phù hợp với các hệ thống cần mức độ cô lập cao hoặc yêu cầu hệ điều hành độc lập. Container phù hợp với các ứng dụng cần triển khai nhanh, tối ưu tài nguyên và mở rộng linh hoạt.

Docker sử dụng Linux Namespaces để cô lập môi trường hoạt động và cgroups để quản lý tài nguyên CPU, RAM.

Trong thực tế, doanh nghiệp có thể kết hợp VPS và Docker để xây dựng hệ thống triển khai phần mềm hiệu quả, ổn định và dễ bảo trì.