# XSS — Stored on /vulnerabilities/xss_s/
# LOW
1.) Target
- Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/xss_s/
- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community
- Security level: low

2.) Tóm tắt 

Truyền payload <script>alert('hwll')</script> vào các trường name và message. Hai giá trị này được lưu thô vào cơ sở dữ liệu và khi trang hiển thị lại thì nội dung không được escape → JavaScript được thực thi trên trình duyệt của client.

3.) PoC (step-by-step)
  1. Intercept request /vulnerabilities/xss_s/.
  2. Thay đổi giá trị txtName và mtxMessage thành payload: <script>alert('hwll')</script>.
  3. Forward request. Khi load trang, alert('hwll') xuất hiện (xuất hiện 2 lần vì cả 2 trường đều chứa payload).
  4. Kết quả PoC cho lỗ hổng Stored XSS:
  ![anh1](images/stored-xss-low.png).

4.) Payload tested

<script>alert('hwll')</script>

5.) Phân tích source code

- Hai trường name và message được lưu thô vào database và chèn trực tiếp vào trang HTML khi hiển thị, không có bước HTML-escaping hay lọc input. Do đó payload <script>alert('hwll')</script> sẽ được lưu vào DB và thực thi trên trình duyệt của người dùng (Stored XSS)

# MEDIUM
1.) Target
- Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/xss_s/
- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community
- Security level: medium

2.) Tóm tắt 

<script>...</script> bị strip/escaped (tại chỗ xử lý server) nên không thực thi. Tuy nhiên lọc hiện tại không loại trừ các thẻ/attribute nguy hiểm khác (như <img onerror=...>), nên có thể dùng attribute event để thực thi JS. Kết quả: do xử lý không đồng nhất/không whitelisting, ứng dụng vẫn bị Stored XSS qua trường name.

3.) PoC (step-by-step)
  1. Intercept request /vulnerabilities/xss_s/.
  2. Thay đổi giá trị txtName và mtxMessage thành payload: <script>alert('hwll')</script>.
  3. Forward request → mở Response quan sát, thấy thẻ <script> của trường name(thẻ mở bị xóa) nên payload không chạy -> có thể thay các biến thể viết hoa, có khoảng trắng, attribute khác, hoặc các event handler vào thử.      Còn tham số message hiển thị alert(\&#039;hwllnah\&#039;) -> đã escape ký tự đặc biệt HTML, chuyển <, > và ' thành &lt;, &gt;, &#039;, nên script không còn thực thi được nữa.
  4. Intercept request /vulnerabilities/xss_s/ lần nữa
  5. Thay đổi giá trị txtName thành payload: <img src=x.png onerror=alert('hwll')>
  6. Forward request. Khi load trang, alert('hwll') xuất hiện.
  7. Kết quả PoC cho lỗ hổng Stored XSS:
  ![anh2](images/stored-xss-medium.png).

4.) Payload tested

<img src=x.png onerror=alert('hwll')>

5.) Phân tích source code
$name = str_replace( '<script>', '', $name );
//$name chỉ str_replace('<script>', '', $name). Điều này chỉ loại bỏ chính xác chuỗi "<script>" chứ không loại bỏ </script>, không loại bỏ event attributes (onerror), không loại bỏ tag khác như <img> hay <svg onload=...>.

# HIGH
1.) Target
- Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/xss_s/
- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community
- Security level: high

2.) Tóm tắt 

Truyền payload <script>alert('hwll')</script> vào các trường name và message. Hai giá trị này được lưu thô vào cơ sở dữ liệu và khi trang hiển thị lại thì nội dung không được escape → JavaScript được thực thi trên trình duyệt của client.

3.) PoC (step-by-step)
  1. Intercept request /vulnerabilities/xss_s/.
  2. Thay đổi giá trị txtName và mtxMessage thành payload: <script>alert('hwll')</script>.
  3. Forward request → mở Response quan sát, thấy thẻ <script> của trường name(thẻ mở bị xóa) nên payload không chạy -> có thể thay các biến thể viết hoa, có khoảng trắng, attribute khác, hoặc các event handler vào thử.      Còn tham số message hiển thị alert(\&#039;hwllnah\&#039;) -> đã escape ký tự đặc biệt HTML, chuyển <, > và ' thành &lt;, &gt;, &#039;, nên script không còn thực thi được nữa.
  4. Intercept request /vulnerabilities/xss_s/ lần nữa
  5. Thay đổi giá trị txtName thành payload: <img src=x.png onerror=alert('hwll')>
  6. Forward request. Khi load trang, alert('hwll') xuất hiện.
  7. Kết quả PoC cho lỗ hổng Stored XSS:
  ![anh3](images/stored-xss-high.png).

4.) Payload tested

<img src=x.png onerror=alert('hwll')>

5.) Phân tích source code
$name = str_replace( '<script>', '', $name );
//$name chỉ str_replace('<script>', '', $name). Điều này chỉ loại bỏ chính xác chuỗi "<script>" chứ không loại bỏ </script>, không loại bỏ event attributes (onerror), không loại bỏ tag khác như <img> hay <svg onload=...>.
# Alert img
![anh4](images/storedxss-alert.png).
