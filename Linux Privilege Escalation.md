###**What does "privilege escalation" mean?**

privilege escalation là việc leo quyền từ thấp lên cao. Nó có được là nhờ sự khai thác lỗ hổng, lỗi logic và cấu hình sai trên hệ điều hoặc app để có truy cập trái phép vào source bị cấm 

Hướng dẫn Thu thập Thông tin (Enumeration) sau khi Xâm nhập Hệ thống Linux

Enumeration (Liệt kê/Thu thập thông tin) là bước đầu tiên bạn phải thực hiện ngay khi giành được quyền truy cập vào bất kỳ hệ thống nào. Dù bạn vào được hệ thống thông qua lỗ hổng nghiêm trọng với quyền root hay chỉ là một tài khoản đặc quyền thấp, việc hiểu rõ môi trường là cực kỳ quan trọng.

Khác với các máy CTF (Capture The Flag), các dự án Pentest thực tế không kết thúc khi bạn chiếm được một user. Enumeration trong giai đoạn hậu xâm nhập (post-compromise) cũng quan trọng y như giai đoạn trước đó.

1. Thông tin Hệ thống Cơ bản

hostname: Trả về tên máy chủ của mục tiêu. Đôi khi tên này tiết lộ vai trò của máy trong mạng doanh nghiệp (Ví dụ: SQL-PROD-01 cho thấy đây là máy chủ SQL chạy môi trường production).

uname -a: In ra thông tin hệ thống chi tiết về Kernel. Điều này cực kỳ hữu ích khi tìm kiếm các lỗ hổng Kernel tiềm năng để leo thang đặc quyền.

/proc/version: File này cung cấp thông tin về phiên bản Kernel và các dữ liệu bổ sung như trình biên dịch (ví dụ: GCC) có được cài đặt hay không.

/etc/issue: Chứa thông tin về hệ điều hành. Tuy nhiên, file này rất dễ bị quản trị viên thay đổi hoặc tùy chỉnh.

2. Quản lý Tiến trình và Môi trường

Lệnh ps (Process Status)Dùng để xem các tiến trình đang chạy.
- PID: ID duy nhất của tiến trình.
- TTY: Loại terminal người dùng đang sử dụng.
- Time: Thời gian CPU mà tiến trình đã sử dụng.
- CMD: Lệnh hoặc tệp thực thi đang chạy.
Các tham số hữu ích:
- ps -A: Xem tất cả các tiến trình đang chạy.
- ps axjf: Xem dưới dạng cây tiến trình (process tree).
- ps aux: Hiển thị tiến trình của tất cả người dùng (a), hiển thị người dùng thực thi (u) và các tiến trình không gắn với terminal (x).
Biến môi trường
- env: Hiển thị các biến môi trường. Biến PATH có thể chứa các trình biên dịch hoặc ngôn ngữ lập trình (như Python) có thể tận dụng để chạy mã độc hoặc leo thang đặc quyền.

3. Người dùng và Đặc quyền
- sudo -l: Liệt kê tất cả các lệnh mà người dùng hiện tại có thể chạy với quyền sudo. Đây là "mỏ vàng" để leo thang đặc quyền.
- ls -la: Luôn sử dụng tham số -la để liệt kê cả các file ẩn (bắt đầu bằng dấu chấm) và quyền hạn chi tiết. Đừng để lỡ các file như .secret.txt.
- id: Cung cấp cái nhìn tổng quan về cấp độ đặc quyền và tư cách thành viên nhóm của người dùng. Bạn cũng có thể kiểm tra cho người dùng khác: id [username].
- /etc/passwd: Đọc file này để khám phá danh sách người dùng trên hệ thống.Mẹo: Sử dụng grep "home" để lọc ra các người dùng thực tế vì họ thường có thư mục trong /home.
4. Lịch sử và Mạng lưới
- history: Xem các lệnh đã thực hiện trước đó. Đôi khi quản trị viên vô tình để lại mật khẩu hoặc thông tin nhạy cảm tại đây.
- ifconfig / ip addr: Kiểm tra các giao diện mạng. Nếu máy có nhiều card mạng (như eth0, tun0), nó có thể là điểm cầu nối (pivoting) để tấn công vào các mạng nội bộ khác.
- netstat: Kiểm tra các kết nối mạng hiện có:
  netstat -a: Hiển thị tất cả socket.
  netstat -at / netstat -au: Liệt kê các kết nối TCP hoặc UDP.
  netstat -l: Liệt kê các cổng đang ở chế độ "nghe" (listening).
  netstat -ano: Hiển thị tất cả socket, không phân giải tên và hiển thị bộ định thời.
5. Khai thác sức mạnh lệnh find
  Lệnh find là công cụ cực kỳ mạnh mẽ để tìm kiếm các vector leo thang đặc quyền.Mục đích tìm kiếmLệnh ví dụ
  Tìm file theo tên  find . -name flag1.txt
  Tìm thư mục  find / -type d -name config
  Tìm file có quyền 777  find / -type f -perm 0777
  Tìm file của user cụ thể  find /home -user frank
  Tìm file đã thay đổi gần đây  find / -mtime 10 (10 ngày qua)
  Tìm file theo dung lượng  find / -size +100M (Lớn hơn 100MB)
  Lưu ý quan trọng: Để tránh thông tin rác từ các lỗi "Permission Denied", hãy thêm 2>/dev/null vào cuối lệnh:find / -perm -u=s -type f 2>/dev/nullSUID Bit: Lệnh trên dùng để tìm các file có set SUID bit. Các file này sẽ chạy với đặc quyền của chủ sở hữu file (thường là root) thay vì người dùng đang thực thi nó, đây là một hướng leo thang đặc quyền rất phổ biến.

6. Các công cụ hỗ trợ khác

  Hãy dành thời gian làm quen với các công cụ xử lý văn bản như: grep, locate, cut, sort, và awk. Chúng sẽ giúp bạn lọc dữ liệu khổng lồ từ hệ thống thành những thông tin có giá trị.
