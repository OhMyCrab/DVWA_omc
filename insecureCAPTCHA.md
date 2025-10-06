# insecure CAPTCHA on /vulnerabilities/authbypass/
# LOW
1.) Target

Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/captcha/

Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community

Security level: low

2.) Tóm tắt POC

CAPTCHA chỉ được hiển thị ở phía client (HTML/JS) nhưng không được kiểm tra ở phía server. Kẻ tấn công có thể gửi trực tiếp request POST thực hiện chức năng thay đổi mật khẩu mà không cần cung cấp hoặc có giá trị g-recaptcha-response hợp lệ → bypass CAPTCHA.

3.) PoC

1. Intercept request username/password mà không nhập CAPTCHA.

2. Trong body request, thay:
   
`step = 1` → `step = 2`

→ `step=2&password_new=hwllnah&password_conf=hwllnah&g-recaptcha-response=&Change=Change`

3. Forward request.

4. Quan sát thấy server thực hiện thay đổi mật khẩu / trả về 200 OK và thông báo thành công mặc dù không có CAPTCHA hợp lệ → CAPTCHA bị bypass.

<img width="756" height="450" alt="image" src="https://github.com/user-attachments/assets/ba9a6267-6307-436d-85cf-3ac3736fbbf2" />

4.) Phân tích source code

Kiểm tra mã nguồn xử lý POST thấy chỉ verify CAPTCHA ở step == 1 nhưng đổi mật khẩu ở step == 2 không kiểm tra lại → có thể gửi trực tiếp step=2 để bypass.
# MEDIUM 
1.) Target

Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/captcha/

Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community

Security level: medium

2.) Tóm tắt POC

Server không kiểm tra token (g-recaptcha-response) và/hoặc chấp nhận tham số client-side passed_captcha=true, do đó attacker có thể forge/replay request để bypass CAPTCHA.

3.) PoC

1. Gửi 1 lần captcha hợp lệ: tích reCAPTCHA và submit. quan sát request — server trả về request body chứa `step=2&password_new=password&password_conf=password&passed_captcha=true&Change=Change`

<img width="742" height="521" alt="image" src="https://github.com/user-attachments/assets/f1489b16-96b7-4277-848e-9f61af837e4f" />

2. Intercept request username/password mà không nhập CAPTCHA.

3. Trong body request, thay:

`step = 1` → `step = 2` , `g-recaptcha-response=` → `passed_captcha=true`.

→ `step=2&password_new=hwllnah&password_conf=hwllnah&passed_captcha=true&Change=Change`

<img width="1168" height="959" alt="image" src="https://github.com/user-attachments/assets/b22b7df8-3c7d-4220-83ec-a3ce7b1cb00e" />

4.Forward request, quan sát response: server thực hiện đổi mật khẩu / trả về 200 OK & thông báo password changed → chứng minh CAPTCHA bị bypass bằng cách điều chỉnh step và thay tham số (server chấp nhận passed_captcha=true / không ràng buộc token).

<img width="743" height="463" alt="image" src="https://github.com/user-attachments/assets/7ee301ac-429b-4391-8fcb-f0be2e92d14a" />

4.) Phân tích source code

Lỗi insecure CAPTCHA xảy ra do server tin dữ liệu client gửi lên thay vì xác thực lại ở server.

`<input type="hidden" name="step" value="2" />`

`<input type="hidden" name="password_new" value="{$pass_new}" />`

`<input type="hidden" name="password_conf" value="{$pass_conf}" />`

`<input type="hidden" name="passed_captcha" value="true" />`

Server tin dữ liệu từ client → Có thể sửa hoặc tự tạo POST với các giá trị này.

`if( !$_POST['passed_captcha'] ) { ... }`
Kiểm tra dựa trên $_POST['passed_captcha'] → Chỉ cần gửi passed_captcha=true thủ công là qua, bypass CAPTCHA.

