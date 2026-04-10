**Liệt kê thông tin xác thực (Authentication Enumeration)**


Hãy coi bản thân bạn như một thám tử kỹ thuật số. Công việc này không chỉ đơn thuần là thu thập manh mối—mà là hiểu được những manh mối đó tiết lộ điều gì về tính bảo mật của một hệ thống. Đây chính là bản chất của việc liệt kê thông tin xác thực. Nó giống như việc lắp ghép các mảnh của một bức tranh xếp hình hơn là chỉ đánh dấu các mục trong một danh sách kiểm tra.

Liệt kê thông tin xác thực tương tự như việc bóc từng lớp của một củ hành. Bạn gỡ bỏ từng lớp bảo mật của hệ thống để làm lộ ra các hoạt động thực sự bên dưới. Nó không chỉ là các bước kiểm tra định kỳ; mà là việc nhìn ra cách mọi thứ kết nối với nhau.

**Xác định tên người dùng (Username) hợp lệ**


Biết được một tên người dùng hợp lệ cho phép kẻ tấn công chỉ cần tập trung vào mật khẩu. Bạn có thể tìm ra tên người dùng theo nhiều cách khác nhau, chẳng hạn như quan sát cách ứng dụng phản hồi trong quá trình đăng nhập hoặc đặt lại mật khẩu.

Ví dụ: Các thông báo lỗi ghi cụ thể rằng "tài khoản này không tồn tại" hoặc "mật khẩu không chính xác" có thể gợi ý về các tên người dùng hợp lệ, giúp công việc của kẻ tấn công trở nên dễ dàng hơn nhiều.

**Chính sách mật khẩu**


Các nguyên tắc khi tạo mật khẩu có thể cung cấp thông tin giá trị về độ phức tạp của mật khẩu được sử dụng trong ứng dụng. Bằng cách hiểu các chính sách này, kẻ tấn công có thể đánh giá độ phức tạp tiềm năng và điều chỉnh chiến lược cho phù hợp.

Ví dụ, đoạn mã PHP dưới đây sử dụng biểu thức chính quy (regex) để yêu cầu mật khẩu phải bao gồm các ký hiệu, số và chữ cái viết hoa:

PHP
<?php
$password = $_POST['pass']; // Ví dụ 1
$pattern = '/^(?=.*[A-Z])(?=.*\d)(?=.*[\W_]).+$/';
if (preg_match($pattern, $password)) {
    echo "Mật khẩu hợp lệ.";
} else {
    echo "Mật khẩu không hợp lệ. Nó phải chứa ít nhất một chữ cái viết hoa, một chữ số và một ký hiệu.";
}
?>


Trong ví dụ trên, nếu mật khẩu được cung cấp không thỏa mãn chính sách đã định nghĩa trong biến $pattern, ứng dụng sẽ trả về một thông báo lỗi tiết lộ yêu cầu của mã regex. Kẻ tấn công có thể dựa vào đó để tạo ra một từ điển (dictionary) các mật khẩu thỏa mãn đúng chính sách này.

**Các vị trí phổ biến để liệt kê thông tin**


Các ứng dụng web chứa đầy các tính năng giúp người dùng thuận tiện hơn nhưng đồng thời cũng có thể khiến họ gặp rủi ro:

**1. Trang đăng ký (Registration Pages)**


Các ứng dụng web thường làm cho quá trình đăng ký trở nên đơn giản và trực quan bằng cách thông báo ngay lập tức liệu email hoặc tên người dùng đó đã có người sử dụng hay chưa. Mặc dù phản hồi này được thiết kế để cải thiện trải nghiệm người dùng, nhưng nó có thể vô tình phục vụ một mục đích khác.

Nếu một lần thử đăng ký dẫn đến thông báo rằng tên người dùng hoặc email đã được đăng ký, ứng dụng đang ngầm xác nhận sự tồn tại của tài khoản đó. Kẻ tấn công khai thác tính năng này bằng cách thử các tên người dùng tiềm năng, từ đó lập ra một danh sách người dùng đang hoạt động mà không cần truy cập trực tiếp vào cơ sở dữ liệu.

**2. Tính năng đặt lại mật khẩu (Password Reset)**


Cơ chế đặt lại mật khẩu được thiết kế để giúp người dùng lấy lại quyền truy cập bằng cách nhập chi tiết tài khoản để nhận hướng dẫn. Tuy nhiên, sự khác biệt trong phản hồi của ứng dụng có thể vô tình tiết lộ thông tin nhạy cảm.

Ví dụ, những thay đổi nhỏ trong phản hồi về việc tên người dùng có tồn tại hay không có thể giúp kẻ tấn công xác minh danh tính người dùng. Bằng cách phân tích các phản hồi này, kẻ tấn công có thể tinh chỉnh danh sách tên người dùng hợp lệ, nâng cao đáng kể hiệu quả của các cuộc tấn công sau đó.

**3. Thông báo lỗi chi tiết (Verbose Errors)**


Các thông báo lỗi quá chi tiết trong quá trình đăng nhập hoặc các quy trình tương tác khác có thể tiết lộ quá nhiều thông tin. Khi các thông báo này phân biệt rõ ràng giữa "không tìm thấy tên người dùng" và "mật khẩu không đúng", chúng giúp người dùng hiểu vấn đề của mình, nhưng đồng thời cung cấp cho kẻ tấn công manh mối xác thực về các tên người dùng tồn tại.

**4. Thông tin từ các vụ rò rỉ dữ liệu (Data Breach Information)**


Dữ liệu từ các vụ vi phạm bảo mật trước đây là một "mỏ vàng" cho kẻ tấn công. Nó cho phép chúng kiểm tra xem tên người dùng và mật khẩu bị rò rỉ có được sử dụng lại trên các nền tảng khác hay không (tấn công Credential Stuffing).

Nếu kẻ tấn công tìm thấy một kết quả khớp, điều đó cho thấy không chỉ tên người dùng được tái sử dụng mà cả mật khẩu cũng có thể bị dùng lại. Kỹ thuật này cho thấy tác động của một vụ rò rỉ dữ liệu đơn lẻ có thể lan rộng đến nhiều nền tảng khác nhau như thế nào.

**Hiểu về Thông báo lỗi chi tiết (Verbose Errors)**

Hãy tưởng tượng bạn là một thám tử có khả năng phát hiện ra những manh mối mà người khác thường bỏ qua. Trong thế giới phát triển web, thông báo lỗi chi tiết (verbose errors) giống như những lời thì thầm vô tình của hệ thống, hé lộ những bí mật đáng lẽ phải được giữ kín.

Những thông báo lỗi chi tiết này vô cùng quý giá trong quá trình gỡ lỗi (debugging), giúp các lập trình viên hiểu chính xác điều gì đã xảy ra. Tuy nhiên, giống như một cuộc trò chuyện bị nghe lén có thể tiết lộ quá nhiều thứ, các lỗi chi tiết này có thể vô tình để lộ dữ liệu nhạy cảm cho những ai "biết cách lắng nghe".

Thông báo lỗi chi tiết có thể trở thành một "mỏ vàng" thông tin, cung cấp các thông tin như:

- Đường dẫn nội bộ (Internal Paths): Giống như một bản đồ dẫn đến kho báu, chúng tiết lộ đường dẫn tệp và cấu trúc thư mục của máy chủ ứng dụng, nơi có thể chứa các tệp cấu hình hoặc mã khóa bí mật mà người dùng bình thường không thể nhìn thấy.

- Chi tiết về Cơ sở dữ liệu: Cung cấp một cái nhìn thoáng qua vào bên trong cơ sở dữ liệu, những lỗi này có thể làm rò rỉ tên bảng và chi tiết các cột.

- Thông tin người dùng: Đôi khi, các lỗi này thậm chí có thể gợi ý về tên người dùng hoặc dữ liệu cá nhân khác, cung cấp các manh mối quan trọng cho các cuộc điều tra sâu hơn.

**Cách kích hoạt Thông báo lỗi chi tiết**

Những kẻ tấn công thường tìm cách kích hoạt các thông báo lỗi chi tiết để ép ứng dụng phải tiết lộ bí mật. Dưới đây là một số kỹ thuật phổ biến được sử dụng để "khiêu khích" các lỗi này:

1. Thử đăng nhập sai (Invalid Login Attempts): Việc này giống như gõ cửa từng nhà để xem cánh cửa nào sẽ mở. Bằng cách cố tình nhập sai tên người dùng hoặc mật khẩu, kẻ tấn công có thể kích hoạt các thông báo lỗi giúp phân biệt giữa tên người dùng hợp lệ và không hợp lệ.
Ví dụ: Nhập một tên người dùng không tồn tại có thể kích hoạt thông báo lỗi khác với khi nhập một tên người dùng có tồn tại nhưng sai mật khẩu.

2. SQL Injection: Kỹ thuật này liên quan đến việc chèn các lệnh SQL độc hại vào các trường nhập liệu, với hy vọng hệ thống sẽ "vấp ngã" và tiết lộ thông tin về cấu trúc cơ sở dữ liệu.
Ví dụ: Đặt một dấu nháy đơn (') vào trường đăng nhập có thể khiến cơ sở dữ liệu gặp lỗi, vô tình làm lộ chi tiết về lược đồ (schema) của nó.

3. Chèn tệp / Duyệt đường dẫn (File Inclusion/Path Traversal): Bằng cách thao túng đường dẫn tệp, kẻ tấn công có thể cố gắng truy cập các tệp bị hạn chế, khiến hệ thống đưa ra các lỗi tiết lộ đường dẫn nội bộ.
Ví dụ: Sử dụng các chuỗi ký tự như ../../ có thể dẫn đến các lỗi làm lộ đường dẫn của các tệp nhạy cảm.

4. Thao túng biểu mẫu (Form Manipulation): Thay đổi các trường hoặc tham số của biểu mẫu có thể đánh lừa ứng dụng hiển thị các lỗi tiết lộ logic xử lý ở hậu trường (backend) hoặc thông tin nhạy cảm của người dùng.
Ví dụ: Thay đổi các trường ẩn (hidden fields) trong biểu mẫu để kích hoạt lỗi xác thực có thể tiết lộ định dạng hoặc cấu trúc dữ liệu mà hệ thống mong đợi.

5. Fuzzing ứng dụng: Gửi các dữ liệu đầu vào không mong muốn đến các phần khác nhau của ứng dụng để xem nó phản ứng thế nào. Các công cụ như Burp Suite Intruder thường được sử dụng để tự động hóa quá trình này, "dội bom" ứng dụng bằng nhiều loại dữ liệu (payloads) khác nhau để xem cái nào sẽ kích hoạt các thông báo lỗi có giá trị.

**Vai trò của Liệt kê (Enumeration) và Tấn công vét cạn (Brute Forcing)**

Khi nói đến việc xâm nhập cơ chế xác thực, liệt kê thông tin và tấn công vét cạn thường đi đôi với nhau:

- Liệt kê người dùng (User Enumeration): Việc tìm ra các tên người dùng hợp lệ sẽ tạo tiền đề, giúp giảm bớt việc đoán mò trong các cuộc tấn công brute-force sau đó.

- Khai thác lỗi chi tiết: Những hiểu biết thu được từ các thông báo lỗi này có thể làm sáng tỏ các khía cạnh như chính sách mật khẩu và cơ chế khóa tài khoản, mở đường cho các chiến lược brute-force hiệu quả hơn.

**Tóm lại**: Thông báo lỗi chi tiết giống như những mẩu bánh mì vụn dẫn dắt kẻ tấn công đi sâu hơn vào hệ thống, cung cấp cho chúng những hiểu biết cần thiết để điều chỉnh chiến lược và có khả năng xâm phạm bảo mật theo những cách mà không bị phát hiện cho đến khi quá muộn.


**Các lỗ hổng trong quy trình đặt lại mật khẩu**

Cơ chế đặt lại mật khẩu là một phần quan trọng để đảm bảo sự thuận tiện cho người dùng trong các ứng dụng web hiện đại. Tuy nhiên, việc triển khai chúng đòi hỏi những cân nhắc bảo mật kỹ lưỡng vì các quy trình đặt lại mật khẩu được bảo mật kém có thể dễ dàng bị khai thác.

**Các phương thức đặt lại mật khẩu phổ biến**

- Đặt lại dựa trên Email (Email-Based Reset):
Khi người dùng yêu cầu đặt lại mật khẩu, ứng dụng sẽ gửi một email chứa liên kết (link) đặt lại hoặc một mã thông báo (token) đến địa chỉ email đã đăng ký. Người dùng nhấp vào liên kết này để dẫn đến trang nhập mật khẩu mới, hoặc hệ thống sẽ tự động tạo mật khẩu mới. Phương pháp này phụ thuộc hoàn toàn vào tính bảo mật của tài khoản email người dùng và tính bí mật của token được gửi đi.

- Đặt lại dựa trên Câu hỏi Bảo mật (Security Question-Based Reset):
Phương pháp này yêu cầu người dùng trả lời các câu hỏi bảo mật đã thiết lập trước đó. Nếu trả lời đúng, hệ thống cho phép tiến hành đặt lại mật khẩu. Mặc dù nó thêm một lớp bảo mật dựa trên thông tin cá nhân, nhưng nó có rủi ro lớn nếu kẻ tấn công thu thập được Thông tin định danh cá nhân (PII) của bạn qua mạng xã hội hoặc các nguồn công khai khác.

- Đặt lại dựa trên SMS (SMS-Based Reset):
Hoạt động tương tự email nhưng mã xác thực được gửi trực tiếp đến điện thoại di động. Phương pháp này giả định rằng quyền truy cập điện thoại là an toàn, nhưng thực tế nó vẫn có thể bị tổn thương bởi các cuộc tấn công SIM swapping (hoán đổi SIM) hoặc đánh chặn tin nhắn.

**Các lỗ hổng thường gặp**

Mỗi phương pháp trên đều có những điểm yếu tiềm ẩn mà bạn cần lưu ý:

1. Token có thể đoán trước (Predictable Tokens):
Nếu các token đặt lại trong liên kết hoặc SMS tuân theo một quy luật tuần tự hoặc quá đơn giản, kẻ tấn công có thể dùng phương pháp tấn công vét cạn (brute-force) để tạo ra các URL đặt lại mật khẩu hợp lệ của người dùng khác.

2. Vấn đề về thời gian hết hạn của Token (Token Expiration Issues):
Token có hiệu lực quá lâu hoặc không mất đi ngay sau khi sử dụng sẽ tạo ra một "cửa sổ cơ hội" cho kẻ tấn công. Một quy trình an toàn đòi hỏi token phải hết hạn nhanh chóng và chỉ sử dụng được duy nhất một lần.

3. Xác thực không đầy đủ (Insufficient Validation):
Các cơ chế xác minh như câu hỏi bảo mật quá phổ biến (ví dụ: "Món ăn yêu thích của bạn là gì?") rất dễ bị đoán. Ngoài ra, nếu việc xác thực chỉ dựa vào email mà tài khoản email đó đã bị xâm nhập, thì toàn bộ hệ thống coi như sụp đổ.

4. Tiết lộ thông tin (Information Disclosure):
Các thông báo lỗi chi tiết như "Email này chưa được đăng ký" hoặc "Chúng tôi đã gửi link đặt lại đến [tên người dùng]" giúp kẻ tấn công xác nhận được tài khoản nào thực sự tồn tại trong hệ thống (User Enumeration).

5. Truyền tải không an toàn (Insecure Transport):
Nếu liên kết hoặc token được gửi qua kết nối không mã hóa (không phải HTTPS), chúng có thể bị kẻ xấu "nghe lén" trên đường truyền mạng.

**Xác thực Cơ bản (Basic Authentication) vào năm 2024?**

Xác thực cơ bản cung cấp một phương pháp đơn giản hơn để bảo mật quyền truy cập vào các thiết bị. Nó chỉ yêu cầu tên người dùng và mật khẩu, giúp việc triển khai và quản lý trở nên dễ dàng trên các thiết bị có khả năng xử lý hạn chế. Các thiết bị mạng như router thường sử dụng xác thực cơ bản để kiểm soát quyền truy cập vào giao diện quản trị của chúng. Trong kịch bản này, mục tiêu chính là ngăn chặn truy cập trái phép với cấu hình tối thiểu.

Mặc dù xác thực cơ bản không cung cấp các tính năng bảo mật mạnh mẽ như các giao thức phức tạp hơn (chẳng hạn như OAuth hoặc xác thực dựa trên token), tính đơn giản của nó khiến nó phù hợp với các môi trường không yêu cầu quản lý phiên (session) và theo dõi người dùng, hoặc các môi trường mà những việc này được quản lý theo cách khác.

Ví dụ: Đối với các thiết bị như router — nơi chủ yếu được truy cập để thay đổi cấu hình thay vì sử dụng thường xuyên — thì chi phí vận hành (overhead) để duy trì các trạng thái phiên là không cần thiết và có thể làm phức tạp hóa hiệu suất thiết bị.

**Đặc điểm kỹ thuật và Cơ chế hoạt động**

Xác thực cơ bản HTTP được định nghĩa trong RFC 7617, quy định rằng các thông tin xác thực (tên người dùng và mật khẩu) phải được vận chuyển dưới dạng một chuỗi mã hóa Base64 bên trong tiêu đề (header) Authorization của HTTP.

- Tính đơn giản nhưng rủi ro: Phương pháp này rất thẳng thắn nhưng không an toàn nếu truyền qua các kết nối không phải HTTPS. Lý do là vì Base64 không phải là một phương pháp mã hóa (encryption) mà chỉ là một cách biến đổi định dạng dữ liệu, nên nó có thể bị giải mã cực kỳ dễ dàng.

- Mối đe dọa thực sự: Nguy cơ lớn nhất thường đến từ việc người dùng sử dụng các thông tin xác thực yếu, vốn có thể bị tấn công vét cạn (brute-forced).

**Cấu trúc tiêu đề Authorization**

HTTP Basic Authentication cung cấp một cơ chế "thách thức - phản hồi" (challenge-response) đơn giản để yêu cầu thông tin xác thực từ người dùng. Định dạng của tiêu đề Authorization như sau:

Authorization: Basic <credentials>

Trong đó, <credentials> là chuỗi mã hóa Base64 của cụm: username:password


**Wayback URLs**

Hãy coi Wayback Machine của Internet Archive (archive.org) như một cỗ máy thời gian. Nó cho phép bạn du hành ngược thời gian để khám phá các phiên bản cũ của trang web, từ đó phát hiện các tệp tin và thư mục không còn hiển thị ở hiện tại nhưng có thể vẫn còn sót lại trên máy chủ. Những "di tích" này đôi khi có thể cung cấp một cửa sau (backdoor) dẫn thẳng vào hệ thống hiện tại.

Ví dụ, sử dụng TryHackMe làm mục tiêu, chúng ta có thể xem lại tất cả các phiên bản cũ của trang web này từ năm 2018 đến nay.

Để trích xuất tất cả các liên kết được lưu trong Wayback Machine, chúng ta có thể sử dụng công cụ có tên là waybackurls. Công cụ này được lưu trữ trên GitHub, bạn có thể dễ dàng cài đặt trên máy của mình bằng các lệnh sau:

Lưu ý: Việc cài đặt công cụ này nằm ngoài phạm vi của bài học. Bạn nên cài đặt nó trên máy ảo (VM) cá nhân của mình.

**Cài đặt waybackurls**

Bash
user@tryhackme $ git clone https://github.com/tomnomnom/waybackurls
user@tryhackme $ cd waybackurls
user@tryhackme $ sudo apt install golang-go -y # Lệnh này là tùy chọn
user@tryhackme $ go build
user@tryhackme $ ls -la
total 6.6M
drwxr-xr-x 4 user user 4.0K Jul  1 18:20 .
drwxr-xr-x 9 user user 4.0K Jul  1 18:20 ..
drwxr-xr-x 8 user user 4.0K Jul  1 18:20 .git
-rw-r--r-- 1 user user   36 Jul  1 18:20 .gitignore
-rw-r--r-- 1 user user  454 Jul  1 18:20 README.mkd
-rw-r--r-- 1 user user   49 Jul  1 18:20 go.mod
-rw-r--r-- 1 user user 5.4K Jul  1 18:20 main.go
drwxr-xr-x 2 user user 4.0K Jul  1 18:20 script
-rwxr-xr-x 1 user user 6.5M Jul  1 18:20 waybackurls

# Chạy công cụ với mục tiêu là tryhackme.com
user@tryhackme $ ./waybackurls tryhackme.com
[-- cắt bớt --]
https://tryhackme.com/.well-known/ai-plugin.json
https://tryhackme.com/.well-known/assetlinks.json
https://tryhackme.com/.well-known/dnt-policy.txt
https://tryhackme.com/.well-known/gpc.json
https://tryhackme.com/.well-known/nodeinfo
https://tryhackme.com/.well-known/openid-configuration
https://tryhackme.com/.well-known/security.txt
https://tryhackme.com/.well-known/trust.txt
[-- cắt bớt --]

**Google Dorks**

Đây là lúc kỹ năng sử dụng công cụ tìm kiếm của bạn tỏa sáng. Bằng cách soạn thảo các truy vấn tìm kiếm cụ thể, được gọi là Google Dorks, bạn có thể tìm thấy thông tin không được định sẵn để công khai. Những truy vấn này có thể lôi ra mọi thứ, từ các thư mục quản trị bị lộ đến các tệp nhật ký (logs) chứa mật khẩu và chỉ mục của các thư mục nhạy cảm.

**Một số ví dụ điển hình:**

- Tìm các bảng quản trị: site:example.com inurl:admin

- Khai quật tệp nhật ký có chứa mật khẩu: filetype:log "password" site:example.com

- Khám phá các thư mục sao lưu (backup): intitle:"index of" "backup" site:example.com

**Kết luận**

Xuyên suốt bài học này, chúng ta đã cùng nhau khám phá các khía cạnh khác nhau của kỹ thuật liệt kê thông tin (enumeration) và tấn công vét cạn (brute force) trên ứng dụng web. Những nội dung này đã trang bị cho bạn kiến thức và kỹ năng thực hành cần thiết để thực hiện các bài đánh giá bảo mật một cách kỹ lưỡng và chuyên nghiệp.

**Những điểm chính cần ghi nhớ**

- Liệt kê thông tin hiệu quả: Việc liệt kê thông tin đúng cách là yếu tố then chốt để xác định các lỗ hổng tiềm ẩn trong ứng dụng web. Sử dụng đúng công cụ và kỹ thuật có thể hé lộ những thông tin giá trị, giúp ích cho việc lập kế hoạch cho các bước tấn công tiếp theo.

- Tối ưu hóa tấn công Brute Force: Để các cuộc tấn công vét cạn đạt hiệu quả cao, bạn cần biết cách tạo ra các danh sách từ (wordlists) thông minh, quản lý tốt các tham số tấn công và khéo léo né tránh các cơ chế phát hiện như giới hạn tốc độ (rate limiting) hay khóa tài khoản.

- Trách nhiệm đạo đức: Đây là điều quan trọng nhất. Luôn thực hiện việc liệt kê và tấn công vét cạn khi và chỉ khi có sự cho phép rõ ràng từ chủ sở hữu hệ thống. Các cuộc tấn công trái phép là hành vi vi phạm pháp luật và có thể dẫn đến những hậu quả pháp lý cực kỳ nghiêm trọng.
