# XSS — Reflected on /vulnerabilities/xss_r/
# LOW
 1) Target
- Title: Reflected XSS on /vulnerabilities/xss_r/
- Target URL: http://127.0.0.1/DVWA-master/vulnerabilities/xss_r/?name=
- Environment: Windows 10, XAMPP Apache/2.4.58, PHP 8.2.12, DVWA vX.Y, Burp Suite Community
- Security level: low / medium / high (ghi rõ khi test)
- Date & Author: 2025-09-29 — Hoàng

2) Tóm tắt
Reflected XSS do input `name` được echo thẳng ra HTML mà không encode. Attack cho phép chạy JS trên browser nạn nhân (steal cookie, CSRF chaining).

3) Recon / How found
- Tools: Burp Proxy (site map), manual inspection of `search` page.
- Steps: proxy → điều hướng → tìm thấy tham số `name` reflected trong response.

4) PoC (step-by-step)
1. Intercept request `/vulnerabilities/xss_r/?name=aaa`.
2. thay đổi param `aaa` với payload: `<script>alert('hwllnah')</script>`.
3. Forward request → alert hwllnah xuất hiện trên browser.
4. Screenshot alert: ![anh1](images/reflected-xss-low.png).

5) Payload tested
  <script>alert('hwllnah')</script>

# MEDIUM
phải url encode mới chạy được khi intercept
"><img src=x onerror=alert('hwllnah')>
