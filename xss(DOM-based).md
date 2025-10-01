
DOM được tạo bằng đối tượng được gọi là document, chứa một thuộc tính được gọi là URL
Khi trình phân tích cú pháp đến mã Javascript, nó sẽ thực thi mã đó và sửa đổi HTML thô của trang
LOW
Trong trường hợp này, mã tham chiếu đến document.URL và do đó, một phần của chuỗi này được nhúng tại thời điểm phân tích cú pháp trong HTML, sau đó được phân tích cú pháp ngay lập tức và mã Javascript được tìm thấy ( alert(…) ) được thực thi trong ngữ cảnh của cùng một trang, do đó có tình trạng XSS.
