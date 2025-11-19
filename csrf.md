# Cross Site Request Forgery (CSRF) on /vulnerabilities/csrf/

# LOW
1.) Target

- Target URL: http://localhost/DVWA-master/vulnerabilities/csrf/#

- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community

- Security level: low

2.) Tóm tắt POC

- Chức năng change password dùng GET cho action nguy hiểm, không có CSRF token, không kiểm tra, xác thực nên có thể gửi link đổi mật khẩu trực tiếp cho nạn nhân

3.) PoC

1. Gửi request change password vào repeater và quan sát

<img width="559" height="349" alt="image" src="https://github.com/user-attachments/assets/d012b4ea-dd5c-4bbc-8f50-58af775837a8" />

<img width="1031" height="489" alt="image" src="https://github.com/user-attachments/assets/2451ef82-9195-42c4-8960-baa68e882330" />

2. Nhận thấy khi thay đổi giá trị 2 tham số password_new và password_conf vẫn hiển  thị thông báo "Password Changed."

<img width="1064" height="711" alt="image" src="https://github.com/user-attachments/assets/25980eba-abc4-4b23-bce3-0bee91630e8d" />

-> khi gửi link http://localhost/DVWA-master/vulnerabilities/csrf/?password_new=hwllnah&password_conf=hwllnah&Change=Change sẽ thay đổi password

3. Đăng nhập tài khoản gordonb và truy cập link http://localhost/DVWA-master/vulnerabilities/csrf/?password_new=hwllnah&password_conf=hwllnah&Change=Change và quan sát kết quả

<img width="604" height="710" alt="image" src="https://github.com/user-attachments/assets/477190bb-8064-4d27-917d-2c3bfcf61ec4" />

4.) Phân tích source code

````
if( isset( $_GET[ 'Change' ] ) ) {
    $pass_new  = $_GET[ 'password_new' ];
    $pass_conf = $_GET[ 'password_conf' ];
````
Lỗ hổng CSRF xảy ra do: Dùng GET cho action nguy hiểm, không có CSRF token, không kiểm tra Origin/Referer, không xác thực lại mật khẩu hiện tại

# MEDIUM
1.) Target

- Target URL: http://localhost/DVWA-master/vulnerabilities/csrf/#

- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community

- Security level: medium

2.) Tóm tắt POC

- Attacker tạo request đổi mật khẩu và thêm header Referer giả:

````
GET /DVWA/vulnerabilities/csrf/?password_new=hacked&password_conf=hacked&Change=Change HTTP/1.1
Host: localhost
Cookie: PHPSESSID=xxxx
Referer: http://localhost/DVWA/vulnerabilities/csrf/
````
-> Server tin rằng request đến từ trang chính → chấp nhận → mật khẩu bị đổi.

3.) PoC 

1. Gửi request change password vào repeater và quan sát

<img width="559" height="349" alt="image" src="https://github.com/user-attachments/assets/d012b4ea-dd5c-4bbc-8f50-58af775837a8" />

<img width="1031" height="489" alt="image" src="https://github.com/user-attachments/assets/2451ef82-9195-42c4-8960-baa68e882330" />

2. Truy cập link http://localhost/DVWA-master/vulnerabilities/csrf/?password_new=hwllnah&password_conf=hwllnah&Change=Change và quan sát kết quả

<img width="923" height="705" alt="image" src="https://github.com/user-attachments/assets/7e977ccd-2cbe-4b64-80fe-8ac07f181602" />

- Kết quả trả về là "That request didn't look correct."

- Có thông báo lỗi

<img width="750" height="36" alt="image" src="https://github.com/user-attachments/assets/dc3a26c9-3cf2-4f51-9d0b-b5d2a3c0281f" />

3. So sánh 2 request change password, nhận thấy request khi truy cập link không có referer, còn request gửi bình thường có referer

<img width="1043" height="455" alt="image" src="https://github.com/user-attachments/assets/e4ff2d55-7b28-4b03-bd41-4ae19638a67d" />

<img width="1181" height="422" alt="image" src="https://github.com/user-attachments/assets/6360c419-9203-48d4-a12d-89f51973c33a" />

4. Chặn request khi truy cập link và thêm referer vào request, gửi và quan sát response

<img width="803" height="614" alt="image" src="https://github.com/user-attachments/assets/001f079e-4611-4c47-a255-ed04a39208fb" />

<img width="1074" height="458" alt="image" src="https://github.com/user-attachments/assets/e539be17-9118-4159-a9d6-07075a52587e" />

4.) Phân tích source code

````
if( isset( $_GET[ 'Change' ] ) ) {
    // Checks to see where the request came from
    if( stripos( $_SERVER[ 'HTTP_REFERER' ] ,$_SERVER[ 'SERVER_NAME' ]) !== false ) {
        // Get input
        $pass_new  = $_GET[ 'password_new' ];
        $pass_conf = $_GET[ 'password_conf' ]; 
````

Lỗ hổng CSRF xảy ra do: Dùng method GET cho hành động nguy hiểm, validation dùng Referer, không có token CSRF
