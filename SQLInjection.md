
Low: code ghép $id trong dấu nháy (WHERE user_id = '$id') → payload bắt đầu bằng dấu nháy (' OR 1=1 --) sẽ đóng dấu nháy rồi chèn OR 1=1 → trả về mọi hàng.

Medium: dev đã bỏ dấu nháy quanh $id (ví dụ WHERE user_id = $id;) và có thể dùng mysqli_real_escape_string() → payload có dấu nháy bị escape hoặc gây lỗi. Thay vì dùng dấu nháy, attacker phải chèn một biểu thức số hợp lệ, ví dụ 1 OR 1=1 --, để câu SQL sau khi ghép trở thành WHERE user_id = 1 OR 1=1 -- → luôn đúng → trả về mọi hàng.
