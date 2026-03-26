###**What does "privilege escalation" mean?**

privilege escalation là việc leo quyền từ thấp lên cao. Nó có được là nhờ sự khai thác lỗ hổng, lỗi logic và cấu hình sai trên hệ điều hoặc app để có truy cập trái phép vào source bị cấm 

###**Hướng dẫn Thu thập Thông tin (Enumeration) sau khi Xâm nhập Hệ thống Linux**

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


**Các Công cụ Thu thập Thông tin Tự động**
Có rất nhiều công cụ có thể giúp ông tiết kiệm thời gian trong quá trình enumeration. Tuy nhiên, lưu ý quan trọng là: Chỉ nên dùng các công cụ này để tiết kiệm thời gian khi ông đã hiểu rõ bản chất, vì chúng vẫn có thể bỏ sót một số vector leo thang đặc quyền (privilege escalation) nhất định.

Dưới đây là danh sách các công cụ enumeration phổ biến trên Linux kèm theo link Github của chúng:

**Lưu ý về môi trường hệ thống**
Môi trường của máy mục tiêu sẽ quyết định ông có thể chạy được tool nào. Ví dụ: Ông không thể chạy một công cụ viết bằng Python nếu máy đó chưa cài Python. Đó là lý do tại sao ông nên làm quen với nhiều loại tool khác nhau thay vì chỉ trung thành với duy nhất một cái "tủ".

- LinPeas: https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS(opens in new tab)
- LinEnum: https://github.com/rebootuser/LinEnum(opens in new tab)(opens in new tab)
- LES (Linux Exploit Suggester): https://github.com/mzet-/linux-exploit-suggester(opens in new tab)
- Linux Smart Enumeration: https://github.com/diego-treitos/linux-smart-enumeration(opens in new tab)
- Linux Priv Checker: https://github.com/linted/linuxprivchecker

**Leo thang Đặc quyền (Privilege Escalation)**
Mục tiêu lý tưởng của việc leo thang đặc quyền là đạt được quyền root. Đôi khi điều này có thể thực hiện được chỉ bằng cách khai thác một lỗ hổng sẵn có, hoặc trong một số trường hợp, bằng cách truy cập vào một tài khoản người dùng khác có nhiều đặc quyền hơn, có nhiều thông tin hơn hoặc có quyền truy cập rộng hơn.

Trừ khi có một lỗ hổng duy nhất dẫn thẳng đến shell của root, quá trình leo thang đặc quyền thường sẽ dựa vào các lỗi cấu hình sai (misconfigurations) và phân quyền lỏng lẻo (lax permissions).

**Khai thác Kernel (Nhân hệ thống)**
Kernel trên các hệ thống Linux quản lý việc giao tiếp giữa các thành phần như bộ nhớ hệ thống và các ứng dụng. Vì chức năng quan trọng này đòi hỏi Kernel phải có các đặc quyền cụ thể; do đó, một cuộc khai thác thành công sẽ tiềm tàng khả năng dẫn thẳng đến quyền root.

Quy trình khai thác Kernel rất đơn giản:
1. Xác định phiên bản Kernel: Sử dụng lệnh uname -a hoặc cat /proc/version.

2. Tìm kiếm mã khai thác (exploit code): Tìm mã tương ứng với phiên bản Kernel của hệ thống mục tiêu.

3. Chạy mã khai thác: Thực thi để chiếm quyền.

**CẢNH BÁO QUAN TRỌNG**: Mặc dù trông có vẻ đơn giản, hãy nhớ rằng một cuộc khai thác Kernel thất bại có thể dẫn đến treo hệ thống (system crash). Hãy đảm bảo rằng kết quả rủi ro này là có thể chấp nhận được trong phạm vi dự án Pentest của ông trước khi thử khai thác Kernel. Trong các kỳ thi như OSCP hay môi trường thực tế, làm sập server là một lỗi cực nặng.

Nguồn nghiên cứu (Research sources)
Dựa trên những thông tin thu thập được, ông có thể sử dụng Google để tìm kiếm mã khai thác hiện có.

Các nguồn như CVE Details cực kỳ hữu ích.

Một lựa chọn khác là sử dụng các script như LES (Linux Exploit Suggester). Tuy nhiên, hãy nhớ rằng các công cụ này có thể tạo ra:

- False positives (Dương tính giả): Báo cáo một lỗ hổng Kernel thực tế không ảnh hưởng đến hệ thống mục tiêu.

- False negatives (Âm tính giả): Không báo cáo bất kỳ lỗ hổng nào mặc dù Kernel thực sự có lỗ hổng.

**Các Gợi ý và Lưu ý (Hints/Notes)**

Đừng quá cụ thể: Khi tìm kiếm exploit trên Google, Exploit-db hoặc searchsploit, đừng quá chi tiết về phiên bản Kernel (ví dụ: thay vì tìm đích danh 4.4.0-116-generic, hãy thử tìm Linux Kernel 4.4.0).

Hiểu mã nguồn TRƯỚC KHI chạy: Hãy chắc chắn ông hiểu cách mã khai thác hoạt động trước khi khởi chạy nó. Một số mã exploit có thể thay đổi hệ điều hành khiến chúng mất an toàn trong quá trình sử dụng tiếp theo hoặc tạo ra những thay đổi không thể đảo ngược, gây ra sự cố về sau.

Trong Lab/CTF: Chuyện này không quá quan trọng.

Trong Pentest thực tế: Đây là điều tuyệt đối cấm kỵ (No-nos).

Tương tác sau khi chạy: Một số exploit yêu cầu tương tác thêm sau khi chạy. Hãy đọc kỹ tất cả các bình luận (comments) và hướng dẫn đi kèm trong mã nguồn.

Chuyển file: Ông có thể chuyển mã khai thác từ máy của mình sang máy mục tiêu bằng cách sử dụng module SimpleHTTPServer của Python (trên máy công) và lệnh wget (trên máy mục tiêu).
