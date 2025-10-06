# Authorization on /vulnerabilities/authbypass/
# LOW
1.) Target
- Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/

- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community

- Security level: low

2.) Tóm tắt POC

- Đăng nhập bằng tài khoản gordonb, sau đó truy cập trực tiếp /authbypass (thay vì qua menu) để dùng chức năng chỉ dành cho admin → Authorization bypass.

3.) PoC (step-by-step)

1. Truy cập `http://127.0.0.1/DVWA-master/vulnerabilities/` với tài khoản gordonb/abc123
2. Nhập vào sau đường dẫn `http://127.0.0.1/DVWA-master/vulnerabilities/` chức năng `/authbypass`
3. Nhấn enter → dùng được chức năng `/authbypass` của admin 
4. Kết quả PoC cho lỗ hổng Authorization-low:
<img width="832" height="890" alt="image" src="https://github.com/user-attachments/assets/3e4cfa03-0375-4b86-9af7-efcb9832f792" />

# MEDIUM
1.) Target
- Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/authbypass/

- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community

- Security level: medium

2.) Tóm tắt POC

- Giao diện HTML chức năng `/authbypass` của người dùng bị khóa nhưng API get_user_data.php vẫn có thể được gọi trực tiếp, nếu API trả dữ liệu → Authorization bypass

3.) Phân tích source code
file medium.php

`if (dvwaCurrentUser() != "admin") {`

`	print "Unauthorised";`

`	http_response_code(403);`

`	exit;`

`}`

- nếu (dvwaCurrentUser() không phải admin sẽ in ra "Unauthorised";

- response trả về là http_response_code(403);

file get_user_data.php

`if ((dvwaSecurityLevelGet() == "high" || dvwaSecurityLevelGet() == "impossible") && dvwaCurrentUser() != "admin") {`

`	print json_encode (array ("result" => "fail", "error" => "Access denied"));`

`	exit;`

`}`

- file get_user_data.php kiểm tra phân quyền chỉ được thực hiện ở mức high và impossible, còn medium và low thì không → người dùng bình thường có thể sử dụng được chức năng này

4.) PoC (step-by-step)

1. Đăng nhập DVWA bằng gordonb / abc123.
2. Nhập vào sau đường dẫn `http://127.0.0.1/DVWA-master/vulnerabilities/authbypass` API `/get_user_data.php`
3. Send request. Quan sát response body. Response trả JSON / HTML chứa thông tin user (user_id, first_name, surname) → Authorization bypass.
4. Kết quả PoC cho lỗ hổng Authorization-medium
<img width="789" height="176" alt="image" src="https://github.com/user-attachments/assets/7c25a984-c3f7-4005-ad3a-f3286bd2dad6" />

# HIGH
1.) Target
- Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/authbypass/

- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community

- Security level: high

2.) Tóm tắt POC

- Ở mức High, cả trang HTML lẫn API GET (get_user_data.php) đã bị khóa khi đăng nhập bằng user. Tuy nhiên, API POST change_user_details.php bị bỏ sót khi kiểm tra phân quyền → user thường có thể gửi POST JSON/form hợp lệ để chỉnh sửa thông tin user khác → Authorization bypass

3.) Phân tích source code (root cause)
file high.php

`if ((dvwaSecurityLevelGet() == "high" || dvwaSecurityLevelGet() == "impossible") && dvwaCurrentUser() != "admin") {`

`	print json_encode (array ("result" => "fail", "error" => "Access denied"));`

`	exit;`

`}`

file change_user_details.php

`if ($_SERVER['REQUEST_METHOD'] != "POST") {`

`	$result = array (`

`						"result" => "fail",`

`						"error" => "Only POST requests are accepted"`

`					);`

`	echo json_encode($result);`

`	exit;`

`}`

`try {`

`	$json = file_get_contents('php://input');`

`	$data = json_decode($json);`

`	if (is_null ($data)) {`

`		$result = array (`

`							"result" => "fail",`

`							"error" => 'Invalid format, expecting "{id: {user ID}, first_name: "{first name}", surname: "{surname}"}'`

`						);`

`		echo json_encode($result);`

`		exit;`

`	}`

`} catch (Exception $e) {`

`	$result = array (`

`						"result" => "fail",`

`						"error" => 'Invalid format, expecting \"{id: {user ID}, first_name: "{first name}", surname: "{surname}\"}'`

`					);`

`	echo json_encode($result);`

`	exit;`

`}`

- high.php chặn HTML cho user

- get_user_data.php kiểm tra phân quyền ở mức high/impossible (nên GET bị khóa ở high)

- change_user_details.php (handler cập nhật) đọc JSON từ php://input, nhưng không thực hiện check dvwaCurrentUser() trước khi cập nhật DB → GET bị chặn nhưng POST vẫn cho phép cập nhật nếu payload hợp lệ.

4.) PoC (Step‑by‑step)

1. Đăng nhập DVWA bằng tài khoản gordonb / abc123
2. Bật Burp Proxy → Intercept
3. Gửi request POST tới change_user_details.php:
<img width="725" height="557" alt="image" src="https://github.com/user-attachments/assets/03720773-8d43-4747-a009-8abc8fff89c4" />

5. Send request. Quan sát response: trả về {"result":"ok"} → Authorization bypass
<img width="647" height="102" alt="image" src="https://github.com/user-attachments/assets/77f475d9-fe13-4ca4-9bdd-e586b6b8225b" />

6. Xác nhận thay đổi: Truy cập trang admin quan sát → id 1: first_name/surname đã đổi thành hwll/nah.
<img width="495" height="292" alt="image" src="https://github.com/user-attachments/assets/4e95c849-4584-46ae-bf5d-db6dd48cf74e" />





