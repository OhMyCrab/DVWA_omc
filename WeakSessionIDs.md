# Weak session ids on /vulnerabilities/weak_id/
- Mục tiêu: phân tích cách session id được tạo ra

# LOW
1.) Target

- Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/vulnerabilities/weak_id/

- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community

- Security level: low

2.) Tóm tắt POC

- Giá trị dvwaSession khi nhấn nút Renegate tăng mỗi lần 1 đơn vị, bắt đầu từ 0

3.) PoC

1.Nhấn generate nhiều lần, quan sát dvwaSession thay đổi

-> mỗi lần nhấn generate dvwaSession sẽ +1 

<img width="1418" height="979" alt="image" src="https://github.com/user-attachments/assets/34b28af0-942f-4f03-8316-b6aa4e4990dc" />

<img width="1207" height="275" alt="image" src="https://github.com/user-attachments/assets/b2b51605-104b-45f0-8931-adc0566a10f3" />

<img width="1188" height="206" alt="image" src="https://github.com/user-attachments/assets/4d5bf3f8-f1cf-443f-9fa5-07963e9052cd" />

5.) Phân tích source code

`<?php`

`$html = "";`

`if ($_SERVER['REQUEST_METHOD'] == "POST") {`

`	if (!isset ($_SESSION['last_session_id'])) {`
  
`		$_SESSION['last_session_id'] = 0;`
    
`	}`
  
`	$_SESSION['last_session_id']++;`
  
`	$cookie_value = $_SESSION['last_session_id'];`
  
`	setcookie("dvwaSession", $cookie_value);`
  
`}`

`?>`

- Giá trị dvwaSession chỉ là một số nguyên tăng dần. Mỗi khi người dùng thực hiện yêu cầu POST (nhấn nút generate), ID phiên tiếp theo là ID phiên hiện tại cộng thêm 1.

# MEDIUM
1.) Target

- Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/vulnerabilities/weak_id/

- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community

- Security level: medium

2.) Tóm tắt POC

- Giá trị dvwaSession khi nhấn nút Renegate sẽ hiển thị thời gian Unix Timestamp (dấu thời gian Unix) ở dạng số nguyên(Unix Timestamp là tổng số giây đã trôi qua kể từ 00:00:00 UTC, ngày 1 tháng 1 năm 1970)

3.) PoC

1.Nhấn generate nhiều lần, quan sát dvwaSession thay đổi

-> mỗi lần nhấn generate dvwaSession sẽ hiển thị một dãy số unix timestamp

<img width="1380" height="989" alt="image" src="https://github.com/user-attachments/assets/9bd5b9e4-eb4d-4a0c-a183-9a47fcec82ed" />

<img width="1279" height="295" alt="image" src="https://github.com/user-attachments/assets/71809789-2295-4a2d-8cde-1816fd3e2575" />

<img width="1414" height="335" alt="image" src="https://github.com/user-attachments/assets/40ab0dcd-dd2f-4d21-aa17-301f330e0dbf" />

4.) Phân tích source code

`<?php`

`$html = "";`

`if ($_SERVER['REQUEST_METHOD'] == "POST") {`

`	$cookie_value = time();`

`	setcookie("dvwaSession", $cookie_value);`

`}`

`?>`
- Hàm time() của PHP trả về Unix Timestamp hiện tại—tổng số giây đã trôi qua kể từ ngày 1 tháng 1 năm 1970. Giá trị này là một số nguyên

# HIGH
1.) Target

- Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/vulnerabilities/weak_id/

- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community

- Security level: high

