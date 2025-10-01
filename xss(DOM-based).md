# DOM based XSS on /vulnerabilities/xss_d/
1. DOM được tạo bằng đối tượng được gọi là document, chứa một thuộc tính được gọi là URL. Khi trình phân tích cú pháp đến mã Javascript, nó sẽ thực thi mã đó và sửa đổi HTML thô của trang.
2. Vì là DOM-based XSS — trang không phụ thuộc vào server để “chèn” payload vào HTML; nó lấy giá trị trực tiếp từ location (thanh địa chỉ) bằng JavaScript ở phía client. Khi chỉnh request trong Burp rồi forward thì chỉ thay đổi request gửi tới server, nhưng thanh địa chỉ của trình duyệt vẫn giữ URL cũ — nên client-side JS (dùng location.search/location.hash) không thấy payload và sẽ không chèn/thi hành. Khi gõ payload vào thanh địa chỉ và load trực tiếp, location chứa payload -> JS client đọc -> document.write/innerHTML chèn vào DOM -> alert hiện.

# LOW
Trong trường hợp này, mã tham chiếu đến document.URL và do đó, một phần của chuỗi này được nhúng tại thời điểm phân tích cú pháp trong HTML, sau đó được phân tích cú pháp ngay lập tức và mã Javascript được tìm thấy ( alert(…) ) được thực thi trong ngữ cảnh của cùng một trang, do đó có tình trạng XSS.
