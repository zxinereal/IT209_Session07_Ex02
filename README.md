# Bài 2 — Quản trị tường lửa UFW cho cụm dịch vụ multi-port

## Mục tiêu cấu hình

Máy chủ chỉ công khai các cổng cần thiết: SSH (`22/tcp`), HTTP (`80/tcp`) và ứng dụng Spring Boot (`8082/tcp`). Cổng MySQL (`3306/tcp`) không có luật `ALLOW`, vì vậy sẽ bị chính sách chặn lưu lượng vào mặc định bảo vệ khỏi Internet.

> Thực hiện các lệnh này trực tiếp trên máy Linux/Ubuntu có UFW. Luôn mở SSH trước khi bật UFW để tránh mất phiên quản trị từ xa.

## Lệnh thực hiện

```bash
# Thiết lập chính sách mặc định
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Chỉ mở các dịch vụ cần truy cập từ Internet
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 8082/tcp

# Kích hoạt tường lửa
sudo ufw enable

# Xem cấu hình chi tiết
sudo ufw status verbose
```

## Kết quả kiểm tra mong đợi

```text
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
80/tcp                     ALLOW IN    Anywhere
8082/tcp                   ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
80/tcp (v6)                ALLOW IN    Anywhere (v6)
8082/tcp (v6)              ALLOW IN    Anywhere (v6)
```

Kết quả thực tế có thể khác ở dòng `Logging` hoặc danh sách IPv6 tùy cấu hình máy. Điều kiện quan trọng là UFW ở trạng thái `active`, ba cổng `22`, `80`, `8082` được phép vào, và **không có** luật cho `3306/tcp`.

## Kiểm tra bổ sung

```bash
# Xác minh không vô tình mở MySQL
sudo ufw status numbered

# (Tùy chọn) xác minh dịch vụ đang lắng nghe trên máy chủ
sudo ss -tlnp | grep -E ':(22|80|8082|3306)\\b'
```
