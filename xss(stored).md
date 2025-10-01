XSS — Stored on /vulnerabilities/xss_s/
Alert 
LOW
1.) Target

Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/xss_s/?name=
Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community
Security level: low
2.) Tóm tắt Truyền payload <script>alert('hwll')</script> vào biến name,message, 2 giá trị này được chèn thô vào trang mà không được escape dẫn tới việc lưu vào database và thực thi mã JavaScript phía client.

3.) PoC (step-by-step)

Intercept request /vulnerabilities/xss_s/
thay đổi param aaa của biến txtName và mtxMessage với payload: <script>alert('hwll')</script>.
Forward request → alert hwllnah xuất hiện 2 lần trên browser.
Kết quả PoC cho lỗ hổng Stored XSS: anh1.
4.) Payload tested

<script>alert('hwllnah')</script>
5.) Phân tích source code source code có dòng: $html .= '

Hello ' . $_GET[ 'name' ] . '
'; //$_GET['name'] lấy trực tiếp dữ liệu từ URL mà không xử lý gì cả.
MEDIUM
