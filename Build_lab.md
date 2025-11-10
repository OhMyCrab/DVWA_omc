Vào trang GitHub của DVWA (digininja/DVWA) → bấm Code → Download ZIP để tải source.

<img width="1094" height="750" alt="image" src="https://github.com/user-attachments/assets/517c8d8e-f66f-4dbd-be48-f99c1a29db50" />

Sau khi tải xong, giải nén file ZIP.

Copy thư mục DVWA-master vào thư mục htdocs trong XAMPP (\xampp\htdocs\).

Mở XAMPP, bật Apache và MySQL.

<img width="583" height="397" alt="image" src="https://github.com/user-attachments/assets/fe949dd0-216d-43c2-a2a0-532337c070a1" />

Truy cập http://localhost/DVWA-master/ để test, thấy báo lỗi kết nối database: DVWA System error - config file not found. Copy config/config.inc.php.dist to config/config.inc.php and configure to your environment. 

<img width="945" height="152" alt="image" src="https://github.com/user-attachments/assets/5cb83e7b-9fa9-4613-8536-19f1fb98a3f5" />

-> truy cập xampp\htdocs\DVWA-master\config, sửa tên file config.inc.php.dist thành config.inc.php

<img width="803" height="177" alt="image" src="https://github.com/user-attachments/assets/221a4526-fee4-41f0-9639-3d05ddff9624" />

Sửa xong lại xuất hiện lỗi mới ở đường dẫn http://localhost/DVWA-master/login.php

Fatal error: Uncaught mysqli_sql_exception: Access denied for user 'dvwa'@'localhost' (using password: YES) in D:\xampp\htdocs\DVWA-master\dvwa\includes\dvwaPage.inc.php:569 Stack trace: #0 D:\xampp\htdocs\DVWA-master\dvwa\includes\dvwaPage.inc.php(569): mysqli_connect('127.0.0.1', 'dvwa', Object(SensitiveParameterValue), '', '3306') #1 D:\xampp\htdocs\DVWA-master\login.php(8): dvwaDatabaseConnect() #2 {main} thrown in D:\xampp\htdocs\DVWA-master\dvwa\includes\dvwaPage.inc.php on line 569.

Mở file config.inc.php, giữ nguyên cấu hình mặc định (user = dvwa, password = dvwa, db = dvwa).

Tiếp tục mở phpMyAdmin (http://localhost/phpmyadmin) → tạo database mới tên là dvwa.

<img width="1235" height="99" alt="image" src="https://github.com/user-attachments/assets/fe7cf03b-caae-4eb0-8c32-da7f4cab6a66" />

Vào tab User accounts trong phpMyAdmin → thêm user mới:

Username: dvwa

Host: localhost

Password: p@ssw0rd

Gán quyền đầy đủ cho database dvwa.

<img width="460" height="183" alt="image" src="https://github.com/user-attachments/assets/8c0eee38-0435-4353-9ba9-870be9336b90" />

Sau khi tạo xong database và user, quay lại trình duyệt → vào http://localhost/DVWA-master/setup.php.

<img width="776" height="906" alt="image" src="https://github.com/user-attachments/assets/5bc744e1-23e9-4ce9-851b-a69858b4e9d5" />

Bấm Create / Reset Database, trang báo thành công.

<img width="442" height="554" alt="image" src="https://github.com/user-attachments/assets/6199793c-7a30-4296-9e63-0500635cdae5" />

Mở lại http://localhost/DVWA-master/login.php → đăng nhập với:

Username: admin

Password: password

Đăng nhập thành công vào giao diện DVWA.

<img width="687" height="160" alt="image" src="https://github.com/user-attachments/assets/08697fc7-858c-45e6-9ee1-8b517355efdc" />

Vào mục DVWA Security trong menu → đổi mức độ bảo mật (Low, Medium, High) để bắt đầu làm lab.
