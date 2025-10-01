# XSS — Stored on /vulnerabilities/xss_s/
# LOW
1.) Target
- Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/xss_s/
- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community
- Security level: low

2.) Tóm tắt 

Truyền payload <script>alert('hwll')</script> vào các tham số name và message. Hai giá trị này được lưu thô vào cơ sở dữ liệu và khi trang hiển thị lại thì nội dung không được escape → JavaScript được thực thi trên trình duyệt của client.

3.) PoC (step-by-step)
  1. Intercept request tới /vulnerabilities/xss_s/.
  2. Thay đổi giá trị txtName và mtxMessage thành payload: <script>alert('hwll')</script>.
  3. Forward request. Khi load trang, alert('hwll') xuất hiện (xuất hiện 2 lần vì cả 2 trường đều chứa payload).
  4. Kết quả PoC cho lỗ hổng Stored XSS:
  ![anh1](images/stored-xss-low.png).

4.) Payload tested
<script>alert('hwll')</script>

5.) Phân tích source code

- Hai tham số name và message được lưu thô vào database và chèn trực tiếp vào trang HTML khi hiển thị, không có bước HTML-escaping hay lọc input. Do đó payload <script>alert('hwll')</script> sẽ được lưu vào DB và thực thi trên trình duyệt của người dùng (Stored XSS)

# MEDIUM
1.) Target
- Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/xss_s/
- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community
- Security level: medium

2.) Tóm tắt 

Truyền payload <script>alert('hwll')</script> vào các tham số name và message. Hai giá trị này được lưu thô vào cơ sở dữ liệu và khi trang hiển thị lại thì nội dung không được escape → JavaScript được thực thi trên trình duyệt của client.

3.) PoC (step-by-step)
  1. Intercept request tới /vulnerabilities/xss_s/.
  2. Thay đổi giá trị txtName và mtxMessage thành payload: <script>alert('hwll')</script>.
  3. Forward request. Khi load trang, alert('hwll') xuất hiện (xuất hiện 2 lần vì cả 2 trường đều chứa payload).
  4. Kết quả PoC cho lỗ hổng Stored XSS:
  ![anh2](images/stored-xss-medium.png).

4.) Payload tested
<script>alert('hwll')</script>

5.) Phân tích source code

- Hai tham số name và message được lưu thô vào database và chèn trực tiếp vào trang HTML khi hiển thị, không có bước HTML-escaping hay lọc input. Do đó payload <script>alert('hwll')</script> sẽ được lưu vào DB và thực thi trên trình duyệt của người dùng (Stored XSS)
# HIGH
# Alert img
![anh3](images/storedxss-alert.png).
