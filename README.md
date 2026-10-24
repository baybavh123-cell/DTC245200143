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

## 3. Kiến trúc hệ thống & Danh mục Dịch vụ

Hệ thống bao gồm **10 container** được tổ chức thành 3 lớp dịch vụ trên 2 mạng `bridge` cô lập (`app-network` và `monitoring`):

| Lớp dịch vụ | Dịch vụ (Container) | Cổng công bố | Chức năng & Mô tả |
| :--- | :--- | :--- | :--- |
| **Web & CSDL** | `nginx-proxy` | `80` / `8443` | Reverse Proxy, tiếp nhận yêu cầu HTTP/HTTPS và điều hướng tới WordPress. |
| **Web & CSDL** | `wordpress-app` | `8080` | Ứng dụng WordPress (PHP 8.2 / Apache) phục vụ website. |
| **Web & CSDL** | `mysql-db` | *Ẩn (Nội bộ)* | Cơ sở dữ liệu MySQL 8.0, chỉ truy cập qua mạng nội bộ `app-network`. |
| **Web & CSDL** | `phpmyadmin` | `8081` | Giao diện quản trị cơ sở dữ liệu MySQL qua trình duyệt. |
| **Giám sát** | `prometheus` | `9090` | Thu thập, lưu trữ số liệu (metrics) theo chuỗi thời gian. |
| **Giám sát** | `grafana` | `3000` | Trực quan hóa số liệu (Dashboards) và truy vấn nhật ký. |
| **Giám sát** | `cadvisor` | `8082` | Thu thập số liệu tài nguyên chi tiết của từng Container. |
| **Giám sát** | `node-exporter` | `9100` | Thu thập thông số tài nguyên hệ thống của máy chủ Host. |
| **Log tập trung** | `loki` | `3100` | Lưu trữ, lập chỉ mục và cung cấp API truy vấn log. |
| **Log tập trung** | `promtail` | *Ẩn (Nội bộ)* | Agent thu thập nhật ký từ hệ thống và đẩy về Loki. |

---

## 4. Nguyên lý hoạt động

1. **Người dùng** gửi yêu cầu truy cập website qua Nginx (Reverse Proxy) ở cổng `80` hoặc `8443`.
2. **Nginx** chuyển tiếp yêu cầu đến dịch vụ WordPress thông qua Docker DNS nội bộ.
3. **WordPress** xử lý nghiệp vụ và tương tác với cơ sở dữ liệu MySQL thông qua mạng `app-network`.
4. **MySQL** trả dữ liệu về WordPress để render trang web.
5. **Dữ liệu phản hồi** được trả ngược về người dùng thông qua Nginx kèm theo các HTTP Security Headers.
6. Các dịch vụ giám sát (`Prometheus`, `Grafana`, `cAdvisor`, `Node-Exporter`) và log (`Loki`, `Promtail`) hoạt động song song trên mạng `monitoring` để thu thập metrics và logs theo thời gian thực.

---

## 5. Cấu trúc thư mục kho mã nguồn

```text
DTC245200143/
├── docker-compose.yml         # Định nghĩa toàn bộ 10 dịch vụ, mạng và volume
├── README.md                  # Tài liệu hướng dẫn vận hành và thông tin đồ án
├── nginx/
│   ├── nginx.conf             # Cấu hình Nginx Reverse Proxy, Security Headers & SSL
│   └── ssl/                   # Thư mục chứa chứng chỉ tự ký (localhost.crt, localhost.key)
└── prometheus/
    └── prometheus.yml         # Cấu hình các Target thu thập metrics cho Prometheus