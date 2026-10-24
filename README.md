# Đồ án Triển khai và Quản trị Hệ thống Phần mềm - Đề 3

* **Sinh viên thực hiện:** Diêm Đăng Phú
* **Mã sinh viên:** DTC245200143
* **Lớp:** K23.K1.CNTT K23I
* **Giảng viên hướng dẫn:** Lê Khánh Dương
* **Kho mã nguồn:** [GitHub Repository](https://github.com/baybavh123-cell/DTC245200143)

---

## 📌 1. Giới thiệu tổng quan

Dự án xây dựng và triển khai một hạ tầng website quảng bá sản phẩm hoàn chỉnh dựa trên nền tảng **WordPress** và **MySQL**, được đóng gói toàn bộ bằng **Docker Compose**. 

Hệ thống tích hợp đầy đủ các lớp dịch vụ hiện đại bao gồm:
* **Nginx Reverse Proxy**: Cổng vào duy nhất, tích hợp SSL/TLS (HTTPS) và cấu hình các tiêu đề bảo mật (security headers).
* **Hệ thống giám sát (Monitoring)**: Sử dụng **Prometheus**, **cAdvisor**, **Node Exporter** và **Grafana** để trực quan hóa hiệu năng container và máy chủ host.
* **Hệ thống nhật ký tập trung (Logging)**: Sử dụng **Loki** và **Promtail** để thu thập, lập chỉ mục và truy vấn log bằng ngôn ngữ LogQL.
* **Tăng cường bảo mật (Hardening)**: Phân tách mạng (Network Isolation), gắn Volume chỉ đọc (`:ro`), mật khẩu độ phức tạp cao, và chính sách tự phục hồi (`restart: unless-stopped`).

---

## 🏗️ 2. Kiến trúc hệ thống & Danh mục Dịch vụ

Hệ thống bao gồm **10 container** được tổ chức thành 3 lớp dịch vụ trên 2 mạng `bridge` cô lập:

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

## 📂 3. Cấu trúc thư mục kho mã nguồn

```text
DTC245200143/
├── docker-compose.yml         # Định nghĩa toàn bộ 10 dịch vụ, mạng và volume
├── README.md                  # Tài liệu hướng dẫn vận hành và thông tin đồ án
├── nginx/
│   ├── nginx.conf             # Cấu hình Nginx Reverse Proxy, Security Headers & SSL
│   └── ssl/                   # Thư mục chứa chứng chỉ tự ký (localhost.crt, localhost.key)
└── prometheus/
    └── prometheus.yml         # Cấu hình các Target thu thập metrics cho Prometheus