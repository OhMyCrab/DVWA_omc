# insecure CAPTCHA on /vulnerabilities/authbypass/
LOW — insecure CAPTCHA (DVWA: /vulnerabilities/captcha/, security = low)
1.) Target

Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/captcha/
Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community
Security level: low

2.) Tóm tắt POC

CAPTCHA chỉ được hiển thị ở phía client (HTML/JS) nhưng không được kiểm tra ở phía server. Kẻ tấn công có thể gửi trực tiếp request POST thực hiện chức năng (ví dụ thay đổi mật khẩu) mà không cần cung cấp hoặc có giá trị g-recaptcha-response hợp lệ → bypass CAPTCHA.

3.) Phân tích (ngắn)

Kiểm tra mã nguồn xử lý POST thấy không có hoặc không thực hiện verify token CAPTCHA với server/Google.

Do đó server chấp nhận request mà không kiểm tra g-recaptcha-response → validation chỉ ở client.

4.) PoC (step-by-step)


2. Bắt request POST thay đổi mật khẩu bằng Burp Proxy → send tới Repeater.

3. Trong body POST, xóa hoàn toàn tham số g-recaptcha-response hoặc đặt nó rỗng:

4. step=1&password_new=password&password_conf=password&g-recaptcha-response=&Change=Change

5. Gửi request.

6. Quan sát thấy server thực hiện thay đổi mật khẩu / trả về 200 OK và thông báo thành công mặc dù không có CAPTCHA hợp lệ → chứng minh CAPTCHA bị bypass.
# MEDIUM 
1.) Target

Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/captcha/

Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community

Security level: medium

2.) Tóm tắt POC

CAPTCHA được sinh và hiển thị, server có kiểm tra token nhưng token không bị vô hiệu hóa sau khi sử dụng (hoặc kiểm tra không đủ chặt), do đó kẻ tấn công có thể tái sử dụng (replay) một token g-recaptcha-response hợp lệ lấy được trước đó để thực hiện nhiều lần hành động được bảo vệ (ví dụ thay đổi mật khẩu) — dẫn đến CAPTCHA bypass bằng replay.

3.) Phân tích source (tóm tắt)


4.) PoC (step-by-step)

1. Gửi 1 lần captcha hợp lệ: tích reCAPTCHA và submit. quan sát request — server trả về request body chứa `step=2&password_new=password&password_conf=password&passed_captcha=true&Change=Change`
<img width="864" height="486" alt="image" src="https://github.com/user-attachments/assets/97dddcb8-d4cc-4231-81da-60cfe711802e" />
2. Intercept request username/password mà không nhập CAPTCHA.

3. Trong body request, thay:

`step = 1` → `step = 2` , `g-recaptcha-response=` → `passed_captcha=true`.

→ `step=2&password_new=hwllnah&password_conf=hwllnah&passed_captcha=true&Change=Change`
<img width="1168" height="959" alt="image" src="https://github.com/user-attachments/assets/b22b7df8-3c7d-4220-83ec-a3ce7b1cb00e" />
4. Quan sát response: server thực hiện đổi mật khẩu / trả về 200 OK & thông báo password changed → chứng minh CAPTCHA bị bypass ở mức MEDIUM bằng cách điều chỉnh step và thay tham số (server chấp nhận passed_captcha=true / không ràng buộc token).

<img width="492" height="160" alt="image" src="https://github.com/user-attachments/assets/42610de5-2fa7-4a9b-9b00-97c94639ca2b" />

