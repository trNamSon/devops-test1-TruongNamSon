# Website Thầy Vũ Đức Hảo

Website giới thiệu dịch vụ đào tạo lái xe ô tô và mô tô của thầy Vũ Đức Hảo tại
Trung tâm Giáo dục nghề nghiệp Vận tải Quảng Nam. Trang cung cấp thông tin khóa
học, quy trình đăng ký tham khảo, bản đồ và các cách liên hệ tư vấn.

## Nội dung website

- Giới thiệu giáo viên và chương trình đào tạo.
- Liên kết gọi điện và nhắn Zalo tới số `090123755`.
- Biểu mẫu đăng ký tư vấn với kiểm tra thông tin cơ bản trên trình duyệt.
- Bản đồ Google Maps và giao diện tương thích với màn hình di động.

> Biểu mẫu hiện chỉ là giao diện minh họa: dữ liệu không được gửi đi hoặc lưu lại.
> Cần kết nối dịch vụ xử lý đăng ký trước khi sử dụng thực tế.

## Cấu trúc

```text
index.html
assets/
├── poster-day-lai-xe.png
└── thay-vu-duc-hao.jpg
```

## Chạy website

Mở `index.html` trong trình duyệt. Trang sử dụng HTML, CSS và JavaScript thuần;
bản đồ cần kết nối Internet.

## CI/CD

Jenkins kiểm tra `index.html` và các ảnh trong `assets/`, sau đó triển khai
website từ thư mục gốc lên Vercel. Pipeline polling SCM mỗi 5 phút để phát hiện
thay đổi; khi có thay đổi trên SCM được cấu hình, Jenkins chạy pipeline và gửi
thông báo trạng thái qua Telegram.

Jenkins cần có các credentials `vercel-token`, `telegram-token` và
`telegram-chat-id`, cùng công cụ NodeJS tên `NodeJS-22`. Địa chỉ website sau
triển khai được cấu hình trong `Jenkinsfile`. Jenkins cần bật quyền truy cập
SCM/GitHub để polling hoạt động.

## Tác giả

- Sinh viên: Trương Nam Sơn
- Lớp: ST23A
