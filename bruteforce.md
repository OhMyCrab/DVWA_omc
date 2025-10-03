# Bruteforce /vulnerabilities/xss_s/
# LOW/MEDIUM/HIGH
- Mục tiêu: Bruteforce mật khẩu của admin

1.) Target
- Target URL: `http://127.0.0.1/DVWA-master/vulnerabilities/brute/?username=admin&password=guest&Login=Login#`
- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community
- Security level: low/medium/high

2.) Tóm tắt PoC

- Gửi request `http://127.0.0.1/DVWA-master/vulnerabilities/brute/?username=admin&password=guest&Login=Login#` sang chức năng Intruder của burp, brute force sniper attack vào tham số guest, dùng wordlist password để check

3.) PoC (step-by-step)
  1. Chuyển request GET tới URL trên và gửi sang Intruder.
  2. Đặt vị trí payload cho tham số `password` (thay `guest`).
  3. Chọn attack type = Sniper; Load Payload configuration: dùng wordlist(500-worst-passwords.txt).
  4. Add Grep - Match dòng Username and/or password incorrect.
  5. Quan sát cột Username and/or password incorrect(hoặc cột length) → thấy dòng khác thường → password đúng
  6. PoC ![anh1](images/brute-force.png).
