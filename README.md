# Đồ án Triển khai và Quản trị Hệ thống Phần mềm - Đề 3

- **Sinh viên thực hiện:** Diêm Đăng Phú
- **Mã sinh viên:** DTC245200143
- **Lớp:** K23.K1.CNTT K23I
- **Giảng viên hướng dẫn:** Lê Khánh Dương
- **Kho mã nguồn:** [GitHub Repository](https://github.com/baybavh123-cell/DTC245200143)

---

## 1. Giới thiệu tổng quan

**Học phần:** Triển khai và Quản trị Hệ thống Phần mềm  
**Đề tài:** Triển khai hệ thống Website Quảng bá Sản phẩm sử dụng Docker Compose, WordPress, MySQL, phpMyAdmin, Nginx Reverse Proxy, Prometheus, Grafana, Loki và Promtail.  

### Mục tiêu
- Xây dựng và triển khai hệ thống website quảng bá sản phẩm hoàn chỉnh bằng WordPress trên nền tảng Docker Compose.
- Áp dụng Nginx làm Reverse Proxy, tích hợp chứng chỉ SSL tự ký (HTTPS) và các tiêu đề bảo mật (Security Headers).
- Tích hợp hệ thống giám sát toàn diện với Prometheus, Grafana, cAdvisor và Node Exporter.
- Xây dựng hệ thống nhật ký tập trung (Centralized Logging) với Loki và Promtail.
- Áp dụng các biện pháp gia cố bảo mật (Hardening) và quản lý mã nguồn chuyên nghiệp trên GitHub.

---

## 2. Công nghệ sử dụng

| Công nghệ | Chức năng |
|---|---|
| Docker | Đóng gói và chạy đa container |
| Docker Compose | Quản lý và điều phối các dịch vụ hạ tầng |
| WordPress (PHP 8.2) | Ứng dụng Web / Website quảng bá sản phẩm |
| MySQL 8.0 | Lưu trữ cơ sở dữ liệu quan hệ |
| phpMyAdmin | Giao diện web quản trị cơ sở dữ liệu MySQL |
| Nginx | Reverse Proxy, xử lý SSL và bảo mật HTTP |
| Git & GitHub | Quản lý phiên bản và lưu trữ mã nguồn |

### Công nghệ giám sát và nhật ký (Monitoring & Logging)
| Công nghệ | Chức năng |
|---|---|
| Prometheus | Thu thập số liệu (Metrics) |
| Grafana | Trực quan hóa số liệu và nhật ký |
| cAdvisor | Giám sát tài nguyên theo từng Container |
| Node Exporter | Giám sát tài nguyên máy chủ Host |
| Loki | Lưu trữ và lập chỉ mục nhật ký tập trung |
| Promtail | Thu thập log từ container đẩy về Loki |
| LogQL | Ngôn ngữ truy vấn nhật ký trong Grafana |

---

## 3. Kiến trúc hệ thống

### Mô hình các lớp dịch vụ (10 Container)
Hệ thống hoạt động trên hai mạng `bridge` độc lập (`app-network` và `monitoring`):

```text
               Người dùng
                   |
                   | (HTTP/HTTPS: 80 / 8443)
                   v
           +---------------+
           |     NGINX     |
           | Reverse Proxy |
           +---------------+
                   |
                   v (app-network)
           +---------------+
           |   WORDPRESS   |
           |   Web App     |
           +---------------+
                   |
                   v
           +---------------+
           |     MYSQL     |
           |   Database    |
           +---------------+
                   ^
                   | (app-network)
           +---------------+
           |  phpMyAdmin   |
           |  Quản trị DB  |
           +---------------+
Nguyên lý hoạt độngNgười dùng gửi yêu cầu qua Nginx (Reverse Proxy).   Nginx chuyển tiếp yêu cầu đến dịch vụ WordPress thông qua Docker DNS nội bộ.   WordPress xử lý nghiệp vụ và tương tác với cơ sở dữ liệu MySQL qua mạng app-network.   Dữ liệu phản hồi được trả ngược về người dùng thông qua Nginx kèm theo các HTTP Security Headers.   Các dịch vụ giám sát (Prometheus, Grafana, cAdvisor, Node-Exporter) và log (Loki, Promtail) hoạt động song song trên mạng monitoring.   4. Cấu trúc thư mục kho mã nguồnPlaintextDTC245200143/
├── docker-compose.yml         # Định nghĩa toàn bộ 10 dịch vụ, mạng và volume
├── README.md                  # Tài liệu hướng dẫn vận hành và thông tin đồ án
├── nginx/
│   ├── nginx.conf             # Cấu hình Nginx Reverse Proxy, Security Headers & SSL
│   └── ssl/                   # Thư mục chứa chứng chỉ tự ký (localhost.crt, localhost.key)
└── prometheus/
    └── prometheus.yml         # Cấu hình các Target thu thập metrics cho Prometheus
Dữ liệu phát sinh được lưu trữ an toàn trong Docker Volumes, không đưa tệp nhạy cảm lên GitHub.   5. Yêu cầu môi trườngHệ điều hành: Windows 11 với WSL 2.   Công cụ: Docker Desktop (chế độ Linux Containers), Docker Compose v2, Git, Google Chrome.   6. Hướng dẫn triển khai & Vận hànhBước 1. Clone Repository từ GitHubMở PowerShell tại thư mục làm việc và chạy[cite: 3]:PowerShellgit clone [https://github.com/baybavh123-cell/DTC245200143.git](https://github.com/baybavh123-cell/DTC245200143.git)
cd DTC245200143
Bước 2. Tạo chứng chỉ SSL cho Nginx (HTTPS tự ký)Chạy lệnh sau trong PowerShell để khởi tạo chứng chỉ[cite: 3]:PowerShellNew-Item -ItemType Directory -Path .\nginx\ssl -Force
docker run --rm -v "${PWD}/nginx/ssl:/certs" alpine/openssl:3.5.9 req -x509 -newkey rsa:2048 -sha256 -noenc -days 365 -keyout /certs/localhost.key -out /certs/localhost.crt -subj "/CN=localhost" -addext "subjectAltName=DNS:localhost,IP:127.0.0.1"
Bước 3. Kiểm tra cấu hình & Khởi chạy hệ thốngPowerShelldocker compose config --quiet
docker compose up -d
Kiểm tra trạng thái các container[cite: 3]:PowerShelldocker compose ps
(Đảm bảo toàn bộ 10 container đều ở trạng thái Running / Up)   7. Danh sách địa chỉ truy cập dịch vụDịch vụĐịa chỉGhi chúWebsite chính (HTTP)http://localhostCổng 80 (Chuyển hướng sang HTTPS)[cite: 3]Website bảo mật (HTTPS)https://localhost:8443Cổng bảo mật SSL[cite: 3]WordPress (Trực tiếp)http://localhost:8080Cổng nội bộ ứng dụng[cite: 3]Quản trị CSDL phpMyAdminhttp://localhost:8081Quản trị CSDL MySQL[cite: 3]Prometheus Dashboardhttp://localhost:9090Mục Status -> Targets   Grafana Dashboard & Logshttp://localhost:3000Dashboards và Explore LogQL (Mặc định admin/admin)   cAdvisor Container Metricshttp://localhost:8082Số liệu tài nguyên container[cite: 3]Node Exporter Host Metricshttp://localhost:9100/metricsSố liệu máy chủ Host[cite: 3]Loki Log Enginehttp://localhost:3100/readyAPI lưu trữ log[cite: 3]8. Cơ chế Bảo mật & HardeningNetwork Isolation (Cô lập mạng): Phân tách thành hai mạng app-network và monitoring. Dịch vụ cơ sở dữ liệu (db) hoàn toàn ẩn khỏi mạng ngoại vi, không mở cổng trực tiếp ra host.   HTTPS & Security Headers: Hỗ trợ TLS 1.2/1.3, cấu hình các header bảo mật (X-Frame-Options, X-Content-Type-Options, Referrer-Policy)[cite: 3].Read-only Volumes (:ro): Các tệp cấu hình quan trọng (nginx.conf, prometheus.yml) được gắn ở chế độ chỉ đọc để ngăn container chỉnh sửa.   Strong Passwords: Thiết lập mật khẩu độ phức tạp cao cho tài khoản root và wpuser của cơ sở dữ liệu.   Restart Policies: Cấu hình restart: unless-stopped giúp dịch vụ tự phục hồi sau sự cố.   Kiểm tra bảo mật các đường dẫn nhạy cảm:PowerShellcurl.exe -k -I https://localhost:8443/.env
curl.exe -k -I https://localhost:8443/.git/config
curl.exe -k -I https://localhost:8443/wp-config.php
(Kết quả mong đợi: Trả về mã lỗi 404)[cite: 3]9. Cấu hình Giám sát & Truy vấn Log (Loki & Promtail)Promtail: Thu thập log từ Docker Containers đẩy về Loki[cite: 3].Grafana Explore: Chọn Data Source loki để truy vấn log bằng ngôn ngữ LogQL[cite: 3]:Xem toàn bộ nhật ký hệ thống:   Đoạn mã{job=~".+"}
Lọc các dòng log chứa thông báo lỗi:   Đoạn mã{job=~".+"} |= "error"
Thống kê tốc độ phát sinh log trong 1 phút:   Đoạn mãsum(rate({job=~".+"}[1m]))
10. Các lệnh quản lý và xử lý sự cốXem log theo thời gian thực của một dịch vụ:PowerShelldocker compose logs -f wordpress
Khởi động lại một dịch vụ cụ thể:PowerShelldocker compose restart nginx
Dừng và dọn dẹp hệ thống (Không dùng -v để tránh mất dữ liệu CSDL):PowerShelldocker compose down
11. Kết luậnĐề tài đã hoàn thành việc xây dựng và vận hành hệ thống website quảng bá sản phẩm nhiều thành phần trên Docker Compose, đáp ứng đầy đủ các tiêu chuẩn kỹ thuật, giám sát, quản lý log và bảo mật của học phần[cite: 3].
---

### Cách cập nhật lên GitHub ngay lập tức:
Bạn chỉ cần mở file `README.md` trong VS Code, **dán toàn bộ nội dung chuẩn ở trên vào**, bấm **`Ctrl + S`** để lưu lại, rồi chạy lệnh đẩy lên Git:
```powershell
git add README.md
git commit -m "docs: fully synchronize README with DTC245200143 project requirements"
git push origin main