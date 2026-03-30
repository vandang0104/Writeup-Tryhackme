**API là gì & Tại sao nó lại quan trọng?**

API là viết tắt của Application Programming Interface (Giao diện Lập trình Ứng dụng). Nó đóng vai trò là một phần mềm trung gian (middleware) tạo điều kiện cho sự giao tiếp giữa hai thành phần phần mềm thông qua một tập hợp các giao thức và định nghĩa.

Trong ngữ cảnh của API:

- Application (Ứng dụng): Ám chỉ bất kỳ phần mềm nào có các chức năng cụ thể.

- Interface (Giao diện): Ám chỉ "hợp đồng dịch vụ" giữa hai ứng dụng, cho phép chúng giao tiếp với nhau thông qua các yêu cầu (requests) và phản hồi (responses).

Tài liệu API (API documentation) chứa tất cả thông tin về cách các nhà phát triển cấu trúc những yêu cầu và phản hồi đó. Tầm quan trọng của API trong phát triển ứng dụng có thể tóm gọn trong một câu: API là những khối thành phần (building blocks) cốt lõi để xây dựng các ứng dụng phức tạp và ở quy mô doanh nghiệp.

Các vụ vi phạm dữ liệu gần đây thông qua API
Việc bảo mật API kém đã dẫn đến nhiều vụ rò rỉ dữ liệu nghiêm trọng trong những năm qua:

**1. Vụ vi phạm dữ liệu LinkedIn (Tháng 6/2021)**

Dữ liệu của hơn 700 triệu người dùng LinkedIn đã bị rao bán trên các diễn đàn web tối (dark web). Hacker đã thu thập (scraped) dữ liệu này bằng cách khai thác API của LinkedIn. Để chứng minh tính xác thực, kẻ tấn công đã công bố mẫu 1 triệu bản ghi bao gồm:

- Họ tên đầy đủ, địa chỉ email, số điện thoại.

- Dữ liệu định vị địa lý.

- Liên kết hồ sơ LinkedIn và thông tin kinh nghiệm làm việc.

Lỗi xác thực người dùng xảy ra như thế nào?
Xác thực người dùng (User authentication) là khía cạnh cốt lõi trong việc phát triển bất kỳ ứng dụng nào chứa dữ liệu nhạy cảm. Lỗi xác thực người dùng (BUA) phản ánh một kịch bản mà tại đó, một điểm cuối (endpoint) của API cho phép kẻ tấn công truy cập vào cơ sở dữ liệu hoặc chiếm được quyền hạn cao hơn so với quyền hạn hiện có.

Nguyên nhân chính đằng sau BUA thường là:

Triển khai xác thực không đúng cách: Ví dụ như sử dụng các truy vấn kiểm tra email/mật khẩu sai lệch.

Thiếu hụt các cơ chế bảo mật: Ví dụ như thiếu các tiêu đề ủy quyền (authorization headers), mã thông báo (tokens), v.v.

Hãy tưởng tượng một kịch bản mà kẻ tấn công có khả năng lạm dụng một API xác thực; điều này cuối cùng sẽ dẫn đến rò rỉ dữ liệu, xóa, sửa đổi dữ liệu hoặc thậm chí là kẻ tấn công chiếm đoạt hoàn toàn tài khoản. Thông thường, các tin tặc sẽ tạo ra các đoạn mã (scripts) đặc biệt để lập hồ sơ, liệt kê người dùng trên hệ thống và xác định các điểm cuối xác thực. Một hệ thống xác thực được triển khai kém có thể khiến bất kỳ người dùng nào cũng có thể mạo danh danh tính của một người dùng khác.

Tác động tiềm tàng
Trong lỗi xác thực người dùng, kẻ tấn công có thể xâm phạm phiên làm việc đã được xác thực hoặc xâm phạm chính cơ chế xác thực để dễ dàng truy cập vào dữ liệu nhạy cảm. Những kẻ xấu có thể giả danh một người được ủy quyền và thực hiện các hoạt động không mong muốn, bao gồm cả việc chiếm đoạt toàn bộ tài khoản.

Ví dụ thực tế (Practical Example)
Tiếp tục sử dụng trình duyệt Chrome và Talend API Tester trên máy ảo (VM) để thực hành gỡ lỗi.

Kịch bản: Bob hiểu rằng xác thực là cực kỳ quan trọng. Anh ấy được giao nhiệm vụ phát triển một endpoint API là apirule2/user/login_v để xác thực người dùng dựa trên email và mật khẩu được cung cấp.

Cơ chế: Endpoint này sẽ trả về một mã token. Mã này sau đó sẽ được gửi kèm trong tiêu đề Authorization-Token (thông qua yêu cầu GET) tới địa chỉ apirule2/user/details để hiển thị thông tin chi tiết của một nhân viên cụ thể.

Lỗ hổng: Bob đã phát triển thành công endpoint đăng nhập; tuy nhiên, anh ấy chỉ sử dụng email để xác nhận người dùng từ bảng dữ liệu (user table) và bỏ qua trường mật khẩu trong câu lệnh truy vấn SQL.

Hậu quả: Kẻ tấn công chỉ cần biết địa chỉ email của nạn nhân là có thể lấy được mã token hợp lệ hoặc chiếm đoạt tài khoản.

Thử nghiệm trên VM:
Bạn có thể kiểm tra điều này bằng cách gửi một yêu cầu POST tới http://localhost:80/MHT/apirule2/user/login_v với các tham số biểu mẫu (form parameters) gồm email và mật khẩu (mật khẩu có thể nhập bất kỳ). Như bạn thấy, endpoint bị lỗi vẫn trả về một mã token, mã này có thể được dùng để truy cập /apirule2/user/details.

Cách khắc phục:
Chúng ta sẽ cập nhật logic truy vấn đăng nhập để sử dụng cả email và mật khẩu để xác thực. Endpoint  /apirule2/user/login_s là một phiên bản an toàn, yêu cầu cả mật khẩu và email chính xác mới cấp quyền cho người dùng.

Các biện pháp giảm thiểu (Mitigation Measures)
Mật khẩu phức tạp: Đảm bảo người dùng cuối sử dụng mật khẩu phức tạp với độ hỗn loạn (entropy) cao.

Không lộ thông tin nhạy cảm: Tuyệt đối không để lộ thông tin đăng nhập nhạy cảm trong các yêu cầu GET hoặc POST (ví dụ: không đưa mật khẩu lên URL).

Sử dụng cơ chế mạnh: Kích hoạt các mã thông báo JSON Web Tokens (JWT) mạnh mẽ, các tiêu đề ủy quyền, v.v.

Đa lớp bảo vệ: Triển khai xác thực đa yếu tố (MFA) nếu có thể, thiết lập tính năng khóa tài khoản hoặc hệ thống captcha để ngăn chặn tấn công vét cạn (brute force).

Mã hóa mật khẩu: Đảm bảo mật khẩu không được lưu dưới dạng văn bản thuần túy (plain text) trong cơ sở dữ liệu để tránh việc kẻ tấn công chiếm đoạt tài khoản sau khi đột nhập vào DB.

- Các chi tiết tài khoản mạng xã hội khác.

**2. Vụ vi phạm dữ liệu Twitter (Tháng 6/2022)**

Dữ liệu của hơn 5,4 triệu người dùng Twitter đã bị tung lên dark web. Hacker thực hiện vụ tấn công này bằng cách khai thác một lỗ hổng zero-day trong API của Twitter, cho phép liên kết tên người dùng (handle) Twitter với số điện thoại hoặc email cá nhân của họ.

**3. Vụ vi phạm dữ liệu PIXLR (Tháng 1/2021)**

PIXLR, một ứng dụng chỉnh sửa ảnh trực tuyến, đã hứng chịu một vụ vi phạm dữ liệu ảnh hưởng đến khoảng 1,9 triệu người dùng. Toàn bộ dữ liệu bị hacker tung lên diễn đàn dark web bao gồm:

- Tên đăng nhập (usernames), địa chỉ email.

- Quốc gia và mật khẩu đã được băm (hashed passwords).

**Lỗi BOLA xảy ra như thế nào?**

Thông thường, các điểm cuối (API endpoints) được sử dụng cho một hoạt động phổ biến là truy xuất và thao tác dữ liệu thông qua các mã định danh đối tượng (object identifiers - ví dụ như ID người dùng).

BOLA thực chất là tên gọi khác của lỗi IDOR (Insecure Direct Object Reference - Tham chiếu đối tượng trực tiếp không an toàn). Lỗi này tạo ra một kịch bản mà người dùng tận dụng chức năng nhập liệu để truy cập vào các tài nguyên (dữ liệu) mà họ không có quyền hạn. Trong một API, các biện pháp kiểm soát này thường được thực hiện thông qua việc lập trình trong phần Models (thuộc kiến trúc Model-View-Controller - MVC) ở cấp độ mã nguồn.

**Tác động tiềm tàng**

Việc thiếu các biện pháp kiểm soát để ngăn chặn truy cập đối tượng trái phép có thể dẫn đến rò rỉ dữ liệu, và trong một số trường hợp là chiếm đoạt hoàn toàn tài khoản (account takeover). Dữ liệu của người dùng hoặc khách hàng trong cơ sở dữ liệu đóng vai trò sống còn đối với uy tín thương hiệu của một tổ chức; nếu dữ liệu này bị rò rỉ trên Internet, nó có thể gây ra những tổn thất tài chính vô cùng lớn.

**Ví dụ thực tế (Practical Example)**.
Hãy tưởng tượng bạn đang thao tác trên một Máy ảo (VM) đã mở sẵn trình duyệt Chrome và ứng dụng Talend API Tester để gỡ lỗi (debug) các API.

1. Kịch bản: Bob là lập trình viên API tại Công ty MHT. Anh ấy phát triển một endpoint là /apirule1/users/{ID} để cho phép các ứng dụng khác yêu cầu thông tin bằng cách gửi một ID nhân viên.

2. Thực hiện: Bạn có thể gửi một yêu cầu GET tới địa chỉ: http://localhost:80/MHT/apirule1_v/user/1.

**Vấn đề nằm ở đâu?**

Vấn đề là endpoint này không xác thực bất kỳ cuộc gọi API nào để xác nhận yêu cầu đó có hợp lệ hay không. Hệ thống không hề kiểm tra quyền hạn (authorization) xem người đang gọi API có thực sự được phép xem thông tin của nhân viên đó hay không.

Giải pháp:
Giải pháp khá đơn giản: Bob cần triển khai một cơ chế ủy quyền để xác định ai có thể gọi API truy cập thông tin nhân viên.

- Mục tiêu này đạt được bằng cách sử dụng các mã thông báo truy cập (access tokens) hoặc mã ủy quyền (authorization tokens) đính kèm trong phần Header của yêu cầu.

- Trong ví dụ trên, Bob sẽ thêm một mã token sao cho chỉ những yêu cầu có mã hợp lệ mới có thể gọi tới endpoint này.

- Nếu bạn thêm một Authorization-Token hợp lệ và gọi tới: http://localhost:80/MHT/apirule1_s/user/1, bạn sẽ nhận được kết quả đúng. Ngược lại, mọi cuộc gọi với token không hợp lệ sẽ trả về thông báo lỗi 403 Forbidden (Bị cấm).

**Các biện pháp giảm thiểu (Mitigation Measures)**

- Triển khai cơ chế ủy quyền phù hợp: Xây dựng hệ thống dựa trên các chính sách người dùng (user policies) và phân cấp (hierarchies) một cách chặt chẽ.

- Kiểm soát truy cập nghiêm ngặt: Luôn thực hiện các phương pháp kiểm tra để xác nhận xem người dùng đã đăng nhập có thực sự được phép thực hiện hành động cụ thể đó trên đối tượng đó hay không.

- Sử dụng Token ngẫu nhiên: Khuyến khích sử dụng các giá trị hoàn toàn ngẫu nhiên (kết hợp cơ chế mã hóa và giải mã mạnh) để tạo ra các mã token mà kẻ tấn công gần như không thể dự đoán được.

**Lỗi xác thực người dùng xảy ra như thế nào?**

Xác thực người dùng (User authentication) là khía cạnh cốt lõi trong việc phát triển bất kỳ ứng dụng nào chứa dữ liệu nhạy cảm. Lỗi xác thực người dùng (BUA) phản ánh một kịch bản mà tại đó, một điểm cuối (endpoint) của API cho phép kẻ tấn công truy cập vào cơ sở dữ liệu hoặc chiếm được quyền hạn cao hơn so với quyền hạn hiện có.

Nguyên nhân chính đằng sau BUA thường là:

- Triển khai xác thực không đúng cách: Ví dụ như sử dụng các truy vấn kiểm tra email/mật khẩu sai lệch.

- Thiếu hụt các cơ chế bảo mật: Ví dụ như thiếu các tiêu đề ủy quyền (authorization headers), mã thông báo (tokens), v.v.

Hãy tưởng tượng một kịch bản mà kẻ tấn công có khả năng lạm dụng một API xác thực; điều này cuối cùng sẽ dẫn đến rò rỉ dữ liệu, xóa, sửa đổi dữ liệu hoặc thậm chí là kẻ tấn công chiếm đoạt hoàn toàn tài khoản. Thông thường, các tin tặc sẽ tạo ra các đoạn mã (scripts) đặc biệt để lập hồ sơ, liệt kê người dùng trên hệ thống và xác định các điểm cuối xác thực. Một hệ thống xác thực được triển khai kém có thể khiến bất kỳ người dùng nào cũng có thể mạo danh danh tính của một người dùng khác.

**Tác động tiềm tàng**

Trong lỗi xác thực người dùng, kẻ tấn công có thể xâm phạm phiên làm việc đã được xác thực hoặc xâm phạm chính cơ chế xác thực để dễ dàng truy cập vào dữ liệu nhạy cảm. Những kẻ xấu có thể giả danh một người được ủy quyền và thực hiện các hoạt động không mong muốn, bao gồm cả việc chiếm đoạt toàn bộ tài khoản.

**Ví dụ thực tế (Practical Example)**
Tiếp tục sử dụng trình duyệt Chrome và Talend API Tester trên máy ảo (VM) để thực hành gỡ lỗi.

1. Kịch bản: Bob hiểu rằng xác thực là cực kỳ quan trọng. Anh ấy được giao nhiệm vụ phát triển một endpoint API là apirule2/user/login_v để xác thực người dùng dựa trên email và mật khẩu được cung cấp.

2. Cơ chế: Endpoint này sẽ trả về một mã token. Mã này sau đó sẽ được gửi kèm trong tiêu đề Authorization-Token (thông qua yêu cầu GET) tới địa chỉ apirule2/user/details để hiển thị thông tin chi tiết của một nhân viên cụ thể.

2. Lỗ hổng: Bob đã phát triển thành công endpoint đăng nhập; tuy nhiên, anh ấy chỉ sử dụng email để xác nhận người dùng từ bảng dữ liệu (user table) và bỏ qua trường mật khẩu trong câu lệnh truy vấn SQL.

3. Hậu quả: Kẻ tấn công chỉ cần biết địa chỉ email của nạn nhân là có thể lấy được mã token hợp lệ hoặc chiếm đoạt tài khoản.

**Thử nghiệm trên VM:**
Bạn có thể kiểm tra điều này bằng cách gửi một yêu cầu POST tới http://localhost:80/MHT/apirule2/user/login_v với các tham số biểu mẫu (form parameters) gồm email và mật khẩu (mật khẩu có thể nhập bất kỳ). Như bạn thấy, endpoint bị lỗi vẫn trả về một mã token, mã này có thể được dùng để truy cập /apirule2/user/details.

**Cách khắc phục:**
Chúng ta sẽ cập nhật logic truy vấn đăng nhập để sử dụng cả email và mật khẩu để xác thực. Endpoint  /apirule2/user/login_s là một phiên bản an toàn, yêu cầu cả mật khẩu và email chính xác mới cấp quyền cho người dùng.

**Các biện pháp giảm thiểu (Mitigation Measures)**

- Mật khẩu phức tạp: Đảm bảo người dùng cuối sử dụng mật khẩu phức tạp với độ hỗn loạn (entropy) cao.

- Không lộ thông tin nhạy cảm: Tuyệt đối không để lộ thông tin đăng nhập nhạy cảm trong các yêu cầu GET hoặc POST (ví dụ: không đưa mật khẩu lên URL).

- Sử dụng cơ chế mạnh: Kích hoạt các mã thông báo JSON Web Tokens (JWT) mạnh mẽ, các tiêu đề ủy quyền, v.v.

- Đa lớp bảo vệ: Triển khai xác thực đa yếu tố (MFA) nếu có thể, thiết lập tính năng khóa tài khoản hoặc hệ thống captcha để ngăn chặn tấn công vét cạn (brute force).

- Mã hóa mật khẩu: Đảm bảo mật khẩu không được lưu dưới dạng văn bản thuần túy (plain text) trong cơ sở dữ liệu để tránh việc kẻ tấn công chiếm đoạt tài khoản sau khi đột nhập vào DB.
