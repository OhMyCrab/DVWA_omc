Vào trang GitHub của DVWA (digininja/DVWA) → bấm Code → Download ZIP để tải source.

Sau khi tải xong, giải nén file ZIP.

Copy thư mục DVWA-master vào thư mục htdocs trong XAMPP (\xampp\htdocs\).

Mở XAMPP, bật Apache và MySQL.

Truy cập http://localhost/DVWA-master/ để test, thấy báo lỗi kết nối database: DVWA System error - config file not found. Copy config/config.inc.php.dist to config/config.inc.php and configure to your environment. 

-> truy cập xampp\htdocs\DVWA-master\config, sửa tên file config.inc.php.dist thành config.inc.php

Sửa xong lại xuất hiện lỗi mới ở đường dẫn http://localhost/DVWA-master/login.php

Fatal error: Uncaught mysqli_sql_exception: Access denied for user 'dvwa'@'localhost' (using password: YES) in D:\xampp\htdocs\DVWA-master\dvwa\includes\dvwaPage.inc.php:569 Stack trace: #0 D:\xampp\htdocs\DVWA-master\dvwa\includes\dvwaPage.inc.php(569): mysqli_connect('127.0.0.1', 'dvwa', Object(SensitiveParameterValue), '', '3306') #1 D:\xampp\htdocs\DVWA-master\login.php(8): dvwaDatabaseConnect() #2 {main} thrown in D:\xampp\htdocs\DVWA-master\dvwa\includes\dvwaPage.inc.php on line 569.

Mở file config.inc.php, giữ nguyên cấu hình mặc định (user = dvwa, password = dvwa, db = dvwa).

Tiếp tục mở phpMyAdmin (http://localhost/phpmyadmin) → tạo database mới tên là dvwa.

Vào tab User accounts trong phpMyAdmin → thêm user mới:

Username: dvwa

Host: localhost

Password: p@ssw0rd

Gán quyền đầy đủ cho database dvwa.

Sau khi tạo xong database và user, quay lại trình duyệt → vào http://localhost/DVWA-master/setup.php.

Bấm Create / Reset Database, trang báo thành công.

Mở lại http://localhost/DVWA-master/login.php → đăng nhập với:

Username: admin

Password: password

Đăng nhập thành công vào giao diện DVWA.

Vào mục DVWA Security trong menu → đổi mức độ bảo mật (Low, Medium, High) để bắt đầu làm lab.
