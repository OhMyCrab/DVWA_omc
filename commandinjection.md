# Command Injection on /vulnerabilities/exec/

#LOW

1.) Target

- Target URL: http://localhost/DVWA-master/vulnerabilities/exec/

- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community

- Security level: low

2.) Tóm tắt POC

- Tham số ip từ request được nối trực tiếp vào shell_exec('ping ' . $target) mà không validate hoặc escape nên hacker chèn thêm lệnh bằng các ký tự như ;, |, &&, ||, $(),… để hệ thống thực thi thêm lệnh ngoài ý muốn

3.) POC

1. Truy cập `http://127.0.0.1/DVWA-master/vulnerabilities/sqli/?id=1&Submit=Submit#`
2. Nhập vào ô text payload `8.8.8.8&whoami`.
3. Nhấn nút submit -> trang trả về kết quả

<img width="618" height="342" alt="image" src="https://github.com/user-attachments/assets/6e50aa86-690b-4e0a-80fc-fa21c46786f4" />

4.) Payload tested

`8.8.8.8&whoami`

5.) Phân tích source code

```
<?php

if( isset( $_POST[ 'Submit' ]  ) ) {
    // Get input
    $target = $_REQUEST[ 'ip' ];

    // Determine OS and execute the ping command.
    if( stristr( php_uname( 's' ), 'Windows NT' ) ) {
        // Windows
        $cmd = shell_exec( 'ping  ' . $target );
    }
    else {
        // *nix
        $cmd = shell_exec( 'ping  -c 4 ' . $target );
    }

    // Feedback for the end user
    echo "<pre>{$cmd}</pre>";
}

?> 
```

- Tham số ip được nối thẳng vào shell_exec mà không được validate hoặc escape, dẫn tới khả năng command injection: attacker có thể chèn toán tử shell (|, &, ;, &&,...) để chèn thêm lệnh.
