# Authorization on /vulnerabilities/authbypass/
# LOW
1.) Target
Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/
Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community
Security level: low

2.) Tóm tắt POC

Đăng nhập vào tài khoản gordonb, copy `/authbypass` vào url `http://127.0.0.1/DVWA-master/vulnerabilities/` để truy cập được chức năng chỉ có admin mới được dùng

3.) PoC (step-by-step)

1. Truy cập `http://127.0.0.1/DVWA-master/vulnerabilities/` với tài khoản gordonb/abc123
2. Nhập vào sau đường dẫn `http://127.0.0.1/DVWA-master/vulnerabilities/` chức năng `/authbypass`
3. Nhấn enter → dùng được chức năng của admin 
4. Kết quả PoC cho lỗ hổng Authorization-low:
<img width="832" height="890" alt="image" src="https://github.com/user-attachments/assets/3e4cfa03-0375-4b86-9af7-efcb9832f792" />

# MEDIUM

1.) Target
Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/authbypass/
Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community
Security level: medium

2.) Tóm tắt POC
Giao diện HTML bị khóa nhưng API (endpoint) get_user_data.php vẫn có thể được gọi trực tiếp, nếu API trả dữ liệu admin thì là authorization bypass.

3.) Phân tích source code
medium.php

`if (dvwaCurrentUser() != "admin") {
	print "Unauthorised";
	http_response_code(403);
	exit;
}`

nếu (dvwaCurrentUser() không phải admin sẽ in ra "Unauthorised";

response trả về là http_response_code(403);

get_user_data.php

`if ((dvwaSecurityLevelGet() == "high" || dvwaSecurityLevelGet() == "impossible") && dvwaCurrentUser() != "admin") {
	print json_encode (array ("result" => "fail", "error" => "Access denied"));
	exit;
}`

file get_user_data.php kiểm tra phân quyền chỉ được thực hiện ở mức high và impossible, còn medium và low thì không → người dùng bình thường có thể sử dụng được chức năng này

3.) PoC (step-by-step)

1. Đăng nhập DVWA bằng gordonb / abc123.

2. Mở Burp Proxy → Intercept.

3. copy `/get_user_data.php` vào sau url `http://127.0.0.1/DVWA-master/vulnerabilities/authbypass`
4. 
`http://127.0.0.1/DVWA-master/vulnerabilities/authbypass/get_user_data.php`

5. Send request. Quan sát response body. Response trả JSON / HTML chứa thông tin user (user_id, first_name, surname) → authorization bypass.

4.) Kết quả PoC cho lỗ hổng Authorization-medium
<img width="789" height="176" alt="image" src="https://github.com/user-attachments/assets/7c25a984-c3f7-4005-ad3a-f3286bd2dad6" />

# HIGH
1.) Target

Target URL: `http://127.0.0.1/DVWA-master/vulnerabilities/sqli/?id=1&Submit=Submit#`

Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community

Security level: high

2.) Tóm tắt POC

"Payload `'OR 1=1-- -` là một khai thác làm điều kiện WHERE luôn đúng và vì trang trả dữ liệu trực tiếp nên đây là in‑band SQL Injection.

3.) PoC (step-by-step)

1. Truy cập `http://127.0.0.1/DVWA-master/vulnerabilities/sqli/session-input.php#`
2. Nhập vào ô text payload `'OR 1=1-- -`.
3. Nhấn nút submit -> trang trả về tất cả bản ghi
4. Kết quả PoC cho lỗ hổng  in-band SQL Injection-high:
![anh3](images/SQL-injection-high.png)

4.) Payload tested

`'OR 1=1-- -`

5.) Phân tích source code

`$query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id' LIMIT 1;";`

- Code vẫn ghép $id trong dấu nháy (WHERE user_id = '$id') → payload bắt đầu bằng dấu nháy ('OR 1=1 -- -) sẽ đóng dấu nháy rồi chèn OR 1=1 kèm comment hết các lệnh đằng sau(LIMIT 1) → trả về mọi hàng.

