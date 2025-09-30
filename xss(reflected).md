# XSS — Reflected on /vulnerabilities/xss_r/
# LOW
 1)) Target
- Title: Reflected XSS on /vulnerabilities/xss_r/
- Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/xss_r/?name=
- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community
- Security level: low

2)) Tóm tắt
Reflected XSS do input `name` được echo thẳng ra HTML mà không encode. Attack cho phép chạy JS trên browser nạn nhân (steal cookie, CSRF chaining).

3)) PoC (step-by-step)
1. Intercept request `/vulnerabilities/xss_r/?name=aaa`.
2. thay đổi param `aaa` với payload: `<script>alert('hwllnah')</script>`.
3. Forward request → alert hwllnah xuất hiện trên browser.
4. Screenshot alert: ![anh1](images/reflected-xss-low.png).

4)) Payload tested
  <script>alert('hwllnah')</script>

# MEDIUM
phải url encode mới chạy được khi intercept
1)) Target
- Title: Reflected XSS on /vulnerabilities/xss_r/
- Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/xss_r/?name=
- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community
- Security level: medium

2)) Tóm tắt
Vì server strip (loại bỏ) thẻ <script> nên thẻ mở bị xóa ⇒ payload <script>...</script> không xuất hiện/không được parse => không chạy.
Ý tưởng thay thế: chèn một thẻ khác có handler (không phải <script>) — ví dụ <img> + onerror, hoặc <svg onload> — vì server chỉ strip <script>, vẫn cho phép các thẻ/thuộc tính khác. Khi thẻ bị load/không tìm thấy src thì onerror/onload sẽ chạy JS.

3)) PoC (step-by-step)
1. Intercept request `/vulnerabilities/xss_r/?name=aaa`.
2. thay đổi param `aaa` với payload: `<script>alert('hwllnah')</script>`.
3. Forward request → mở Response → Raw / View Source. Do server strip thẻ <script> (thẻ mở bị xóa) nên payload không chạy.
4. Thử payload bypass (không dùng <script>): `<img src=x onerror=alert('hwllnah')>`
5. URL-encoded: %3Cimg%20src%3D%22x.png%22%20onerror%3D%22alert('hwllnah')%22%3E
6. Forward request → alert hwllnah xuất hiện trên browser.
7. Screenshot alert: ![anh2](images/reflected-xss-medium.png).

5)) Payload tested
<img src=x.png onerror=alert('hwllnah')>
