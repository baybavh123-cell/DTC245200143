Đồ án Triển khai và Quản trị Hệ thống Phần mềm - Đề 3Sinh viên thực hiện: Diêm Đăng PhúMã sinh viên: DTC245200143Lớp: K23.K1.CNTT K23IGiảng viên hướng dẫn: Lê Khánh DươngKho mã nguồn: GitHub Repository1. Giới thiệuHọc phần: Triển khai và Quản trị Hệ thống Phần mềmĐề tài: Triển khai hệ thống Website Quảng bá Sản phẩm/Blog/CMS sử dụng Docker Compose, MySQL, phpMyAdmin, Nginx, Prometheus, Grafana, Loki và Promtail.Mã số sinh viên: DTC245200143Mục tiêuXây dựng và triển khai hệ thống quản lý nội dung Blog/CMS/Website quảng bá sản phẩm bằng WordPress.Hệ thống sử dụng Docker Compose để quản lý các dịch vụ, MySQL để lưu trữ dữ liệu và Nginx làm Reverse Proxy.Tích hợp hệ thống giám sát toàn diện với Prometheus, Grafana, cAdvisor, Node Exporter và hệ thống log tập trung Loki, Promtail.Dự án đồng thời áp dụng các biện pháp gia cố bảo mật (Hardening) và quản lý mã nguồn chuyên nghiệp trên GitHub.2. Công nghệ sử dụngCông nghệChức năngDockerĐóng gói và chạy containerDocker ComposeQuản lý các dịch vụ hạ tầngWordPress (PHP 8.2)Website Blog/CMS / Quảng bá sản phẩmMySQL 8.0 / 8.4Lưu trữ cơ sở dữ liệuphpMyAdminQuản trị MySQL qua giao diện WebNginxReverse Proxy, xử lý SSL và bảo mật HTTPGit & GitHubQuản lý phiên bản và lưu trữ mã nguồnCông nghệ giám sát và nhật ký (Monitoring & Logging)Công nghệChức năngPrometheusThu thập Metrics theo chuỗi thời gianGrafanaTrực quan hóa giám sát và LogscAdvisorGiám sát tài nguyên Docker ContainersNode ExporterGiám sát tài nguyên máy chủ HostLokiLưu trữ và truy vấn Logs tập trungPromtailAgent thu thập Logs từ container đẩy về LokiLogQLNgôn ngữ truy vấn Logs trong Grafana3. Kiến trúc hệ thốngMô hình kiến trúc               Người dùng
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
           |   Blog CMS    |
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
Nguyên lý hoạt độngNgười dùng truy cập website qua Nginx (Reverse Proxy).Nginx chuyển tiếp HTTP Request đến WordPress thông qua Docker DNS nội bộ.WordPress xử lý yêu cầu và truy vấn MySQL qua mạng app-network.MySQL trả dữ liệu về WordPress.WordPress gửi phản hồi thông qua Nginx kèm theo các HTTP Security Headers.Người quản trị sử dụng phpMyAdmin để kiểm tra và quản lý cơ sở dữ liệu.Các dịch vụ giám sát (Prometheus, Grafana, cAdvisor, Node-Exporter) và log (Loki, Promtail) hoạt động song song trên mạng monitoring.4. Chức năng hệ thống Blog/CMSHệ thống sử dụng WordPress với các chức năng chính:Đăng nhập và quản trị website.Tạo và xuất bản bài viết.Chỉnh sửa và xóa bài viết.Quản lý danh mục.Quản lý người dùng.Phân quyền người dùng theo vai trò.Quản lý nội dung và hình ảnh.Tìm kiếm bài viết.5. Cấu trúc thư mục dự ánDTC245200143/
├── docker-compose.yml         # Định nghĩa toàn bộ 10 dịch vụ, mạng và volume
├── .env.example               # Tệp mẫu cấu hình biến môi trường
├── .gitignore                 # Danh sách bỏ qua không đẩy lên Git
├── README.md                  # Tài liệu hướng dẫn vận hành và thông tin đồ án
├── nginx/
│   ├── nginx.conf             # Cấu hình Nginx Reverse Proxy & Security Headers
│   └── ssl/                   # Thư mục chứa chứng chỉ SSL tự ký (localhost.crt, localhost.key)
├── prometheus/
│   └── prometheus.yml         # Cấu hình các Target thu thập metrics cho Prometheus
├── grafana/
│   ├── dashboards/            # Các mẫu Dashboard trực quan
│   └── provisioning/
│       └── datasources/       # Cấu hình tự động kết nối Prometheus & Loki
├── loki/                      # Cấu hình lưu trữ log tập trung
├── promtail/                  # Cấu hình agent thu thập log
├── database/                  # Thư mục chứa script khởi tạo CSDL (nếu có)
├── scripts/                   # Các script hỗ trợ tự động hóa
└── docs/
    └── screenshots/           # Ảnh minh chứng báo cáo đồ án
WordPress sử dụng Docker Image chính thức. Dữ liệu phát sinh trong quá trình sử dụng được lưu trong Docker Volumes, không đưa lên GitHub.6. Yêu cầu môi trườngHệ thống được triển khai và kiểm thử trên:Windows 11.Docker Desktop (chế độ Linux Containers).WSL 2.Docker Compose v2.Git.Google Chrome.7. Hướng dẫn triển khaiBước 1. Clone RepositoryMở PowerShell và chạy:git clone https://github.com/baybavh123-cell/DTC245200143.git
cd DTC245200143
Bước 2. Cấu hình biến môi trườngTạo file .env từ file mẫu:Copy-Item .env.example .env
notepad .env
Thay các mật khẩu mẫu bằng mật khẩu mạnh.Các biến cần thiết:MYSQL_ROOT_PASSWORD=CHANGE_ME_ROOT_PASSWORD
MYSQL_DATABASE=wordpress_db
MYSQL_USER=wpuser
MYSQL_PASSWORD=CHANGE_ME_USER_PASSWORD
Không sử dụng nguyên các mật khẩu mẫu khi triển khai.Bước 3. Kiểm tra Docker Composedocker compose config --quiet
Nếu không có lỗi, tiếp tục triển khai.Bước 4. Khởi động hệ thốngdocker compose up -d
Kiểm tra trạng thái:docker compose ps
Bước 5. Cài đặt WordPress lần đầuTruy cập:
https://localhost:8443 (hoặc http://localhost:8080)Nếu là lần khởi chạy mới, WordPress sẽ hiển thị giao diện cài đặt.Thực hiện:Chọn ngôn ngữ Tiếng Việt.Nhập tên website.Tạo tài khoản quản trị.Thiết lập mật khẩu mạnh.Nhập email quản trị.Hoàn tất cài đặt WordPress.8. Địa chỉ truy cậpDịch vụĐịa chỉGhi chúWebsite Blog/CMS (HTTP)http://localhostCổng 80 (Chuyển hướng sang HTTPS)Website Blog/CMS (HTTPS)https://localhost:8443Cổng bảo mật SSLWordPress (Trực tiếp)http://localhost:8080Cổng nội bộ ứng dụngWordPress Adminhttp://localhost:8080/wp-adminTrang quản trịphpMyAdminhttp://localhost:8081Quản trị CSDL MySQLPrometheushttp://localhost:9090Mục Status -> TargetsGrafanahttp://localhost:3000Dashboards & Explore LogQLcAdvisorhttp://localhost:8082Container MetricsNode Exporterhttp://localhost:9100/metricsHost MetricsLoki Log Enginehttp://localhost:3100/readyAPI lưu trữ logMySQL chỉ được truy cập qua mạng Docker nội bộ.9. Nginx Reverse ProxyNginx được sử dụng để chuyển tiếp HTTP Request đến WordPress.Địa chỉ truy cập chính: https://localhost:8443Nginx chuyển request đến: http://wordpress:80Security HeadersHệ thống sử dụng các HTTP Security Headers:add_header X-Content-Type-Options "nosniff" always;
add_header X-Frame-Options "SAMEORIGIN" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
Kiểm tra bằng PowerShell:curl.exe -k -I https://localhost:8443
10. Bảo mật hệ thốngCác biện pháp bảo mật đã được cấu hình:Sử dụng Nginx Unprivileged Image & Read-only Volumes (:ro).Phân chia mạng app-network và monitoring.Không công khai cổng MySQL ra host.Không công khai trực tiếp cổng WordPress ra mạng ngoài.Sử dụng HTTP Security Headers và chứng chỉ SSL/TLS.Sử dụng mật khẩu riêng độ phức tạp cao cho Database.Không đưa file .env chứa mật khẩu lên GitHub.11. Kiểm tra và quản lý ContainersKiểm tra các dịch vụ:docker compose ps
Xem log:docker compose logs
Xem log WordPress:docker compose logs wordpress
Xem log Nginx:docker compose logs nginx
Dừng hệ thống:docker compose down
Khởi động lại:docker compose up -d
Lưu ý: Không sử dụng tùy chọn -v với lệnh docker compose down nếu chưa sao lưu dữ liệu, vì tùy chọn này có thể xóa Docker Volumes của dự án.12. Các giai đoạn triển khaiCommit 1 – Website và Reverse ProxyTriển khai WordPress.Triển khai MySQL.Tích hợp phpMyAdmin.Cấu hình Nginx Reverse Proxy.Cấu hình Security Headers.Quản lý cấu hình bằng GitHub.Viết README.Commit 2 – MonitoringTriển khai Prometheus.Triển khai Grafana.Tích hợp cAdvisor & Node Exporter.Thiết lập Dashboard giám sát Container, Web Server và Máy chủ.Commit 3 – Centralized LoggingTriển khai Loki.Triển khai Promtail.Tích hợp Loki với Grafana.Truy vấn Logs bằng LogQL.Hoàn thiện Hardening hệ thống.13. Kiểm thửCác nội dung kiểm thử bao gồm:Truy cập website Blog/CMS qua Nginx (HTTP/HTTPS).Đăng nhập WordPress Administrator.Tạo và xuất bản bài viết.Quản lý danh mục.Kiểm tra dữ liệu bài viết bằng phpMyAdmin.Kiểm tra kết nối MySQL.Kiểm tra Nginx Reverse Proxy & Security Headers.Kiểm tra trạng thái Docker Containers (docker compose ps).Kiểm tra Metrics trên Prometheus & Grafana.Kiểm tra Logs trên Loki bằng LogQL.Kiểm tra dữ liệu bền vững sau khi khởi động lại Containers.14. Centralized Logging – Loki & Promtail1. Giới thiệuHệ thống sử dụng Loki và Promtail để thu thập, lưu trữ và truy vấn log tập trung từ các Docker Container.Các thành phần:Promtail: Thu thập log từ Docker Containers.Loki: Tiếp nhận và lưu trữ log.Grafana: Hiển thị và tìm kiếm log bằng LogQL.2. Khởi động hệ thốngChạy:docker compose up -d
Kiểm tra trạng thái:docker compose ps
Kiểm tra Loki:curl.exe -i http://localhost:3100/ready
Kết quả mong đợi: HTTP/1.1 200 OK3. Truy cập GrafanaĐịa chỉ: http://localhost:3000Vào Explore, chọn Data Source Loki.4. Truy vấn LogQLLog WordPress:{job=~".+", container_name="wordpress-app"}
Log MySQL:{job=~".+", container_name="mysql-db"}
Log Nginx:{job=~".+", container_name="nginx-proxy"}
Lọc HTTP GET của Nginx:{job=~".+", container_name="nginx-proxy"} |= "GET"
Lọc các thông báo lỗi của MySQL/WordPress:{job=~".+"} |~ "(?i)error"
5. Kết quả kiểm thửWordPress & Nginx: Đã truy vấn thành công log thực tế trong Grafana Explore.MySQL: Đã ghi nhận log khởi động và tương tác cơ sở dữ liệu.6. Lưu ý bảo mậtLoki chỉ mở cổng 3100 trên 127.0.0.1 của máy chạy Docker.Promtail sử dụng Docker socket để thu thập log.Không đưa file .env chứa mật khẩu thật lên GitHub.15. HTTPS & Security HardeningHTTPSHệ thống sử dụng Nginx Reverse Proxy và chứng chỉ SSL tự ký.HTTP: http://localhost (Chuyển hướng 301 sang HTTPS)HTTPS: https://localhost:8443Hỗ trợ TLS 1.2 và TLS 1.3.Tạo chứng chỉ SSLTrước khi khởi động hệ thống lần đầu, mở PowerShell tại thư mục dự án và chạy:New-Item -ItemType Directory -Path .\nginx\ssl -Force
docker run --rm -v "${PWD}/nginx/ssl:/certs" alpine/openssl:3.5.9 req -x509 -newkey rsa:2048 -sha256 -noenc -days 365 -keyout /certs/localhost.key -out /certs/localhost.crt -subj "/CN=localhost" -addext "subjectAltName=DNS:localhost,IP:127.0.0.1"
Sau đó khởi động:docker compose up -d
Các biện pháp bảo mậtHTTPS với TLS 1.2/1.3.Security Headers: X-Content-Type-Options, X-Frame-Options, Referrer-Policy.Ẩn phiên bản Nginx và header X-Powered-By.Chặn truy cập các tệp/thư mục nhạy cảm: .env, .git và wp-config.php.Phân tách mạng nội bộ (app-network và monitoring).Chuyển hướng tự động HTTP sang HTTPS.Kiểm tra bảo mậtcurl.exe -k -I https://localhost:8443
curl.exe -I http://localhost:8080
curl.exe -k -I https://localhost:8443/.env
curl.exe -k -I https://localhost:8443/.git/config
curl.exe -k -I https://localhost:8443/wp-config.php
Kết quả kiểm thử đã xác nhận HTTPS trả về 200, HTTP chuyển hướng 301 và các đường dẫn nhạy cảm trả về 404.16. Kết luậnDự án đã xây dựng thành công hệ thống Blog/CMS/Website quảng bá sản phẩm hoàn chỉnh, có khả năng triển khai và vận hành bằng Docker Compose.Việc tích hợp Nginx, Prometheus, Grafana, Loki và Promtail giúp hệ thống đáp ứng đầy đủ các yêu cầu về Reverse Proxy, giám sát, quản lý log tập trung và bảo mật trong học phần Triển khai và Quản trị Hệ thống Phần mềm.