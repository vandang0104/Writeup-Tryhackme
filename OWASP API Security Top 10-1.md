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


**Lỗi phơi nhiễm dữ liệu quá mức xảy ra như thế nào?**

Phơi nhiễm dữ liệu quá mức xảy ra khi các ứng dụng có xu hướng tiết lộ nhiều thông tin hơn mức cần thiết cho người dùng thông qua các phản hồi API.

Các nhà phát triển ứng dụng thường để lộ tất cả các thuộc tính của đối tượng (dựa trên cách triển khai chung/mặc định) mà không cân nhắc đến mức độ nhạy cảm của chúng. Họ phó mặc nhiệm vụ lọc dữ liệu cho lập trình viên Front-end thực hiện trước khi hiển thị cho người dùng. Kết quả là, một kẻ tấn công có thể chặn bắt (intercept) phản hồi từ API và dễ dàng trích xuất các dữ liệu bảo mật mong muốn.

Các công cụ phát hiện lỗi lúc thực thi (runtime detection tools) hoặc các công cụ quét bảo mật thông thường có thể đưa ra cảnh báo về lỗ hổng này. Tuy nhiên, chúng không thể phân biệt được đâu là dữ liệu hợp lệ cần được trả về và đâu là dữ liệu nhạy cảm cần được giữ kín.

**Tác động tiềm tàng**

Một kẻ xấu có thể thực hiện hành vi "đánh hơi" (sniffing) lưu lượng truy cập và dễ dàng tiếp cận dữ liệu bí mật, bao gồm các chi tiết cá nhân như: số tài khoản, số điện thoại, mã thông báo truy cập (access tokens) và nhiều thông tin khác. Thông thường, các API trả về các token nhạy cảm mà sau đó có thể được sử dụng để gọi tới các điểm cuối (endpoints) quan trọng khác.

**Ví dụ thực tế (Practical Example)**

Tiếp tục sử dụng trình duyệt Chrome và Talend API Tester trên máy ảo (VM) để thực hành.

1. Kịch bản: Công ty MHT ra mắt một cổng thông tin dựa trên bình luận. Hệ thống nhận bình luận của người dùng và lưu trữ vào cơ sở dữ liệu cùng các thông tin khác như vị trí, thông tin thiết bị, v.v., để cải thiện trải nghiệm người dùng.

2. Sai lầm của Bob: Bob được giao nhiệm vụ phát triển một endpoint để hiển thị bình luận trên trang web chính. Anh ấy đã tạo ra endpoint apirule3/comment_v/{id} để lấy tất cả thông tin có sẵn của một bình luận từ cơ sở dữ liệu. Bob mặc định rằng lập trình viên front-end sẽ tự lọc bỏ những thông tin thừa khi hiển thị.

3. Vấn đề: API đang gửi đi nhiều dữ liệu hơn mức mong muốn. Thay vì dựa dẫm vào kỹ sư front-end, chỉ những dữ liệu thực sự liên quan mới được phép gửi đi từ cơ sở dữ liệu.

**Giải pháp:**
Sau khi nhận ra sai lầm, Bob đã cập nhật và tạo ra một endpoint an toàn là /apirule3/comment_s/{id}. Endpoint này chỉ trả về những thông tin cần thiết nhất cho lập trình viên (như nội dung bình luận và tên người dùng).

**Các biện pháp giảm thiểu (Mitigation Measures)**
- Tuyệt đối không phó mặc việc lọc dữ liệu nhạy cảm cho lập trình viên front-end. Việc lọc phải được thực hiện ở phía máy chủ (Backend).

- Rà soát định kỳ: Đảm bảo kiểm tra thường xuyên các phản hồi từ API để đảm bảo nó chỉ trả về dữ liệu hợp lệ và không gây ra bất kỳ vấn đề bảo mật nào.

- Tránh sử dụng các phương thức chung chung: Hạn chế dùng các hàm như to_string() hoặc to_json() trên toàn bộ đối tượng dữ liệu vì chúng sẽ xuất bản tất cả các thuộc tính của đối tượng đó.

- Kiểm thử API (API Endpoint Testing): Sử dụng nhiều trường hợp kiểm thử (test cases) khác nhau và xác minh thông qua cả kiểm thử tự động lẫn thủ công để xem liệu API có đang rò rỉ thêm dữ liệu thừa hay không.


**Lỗi này xảy ra như thế nào?**

Việc thiếu hụt tài nguyên và giới hạn tần suất có nghĩa là các API không áp dụng bất kỳ hạn chế nào đối với tần suất khách hàng yêu cầu tài nguyên hoặc kích thước của các tệp tin được gửi lên. Điều này ảnh hưởng xấu đến hiệu suất của máy chủ API và dẫn đến tình trạng DoS (Denial of Service - Từ chối dịch vụ) hoặc khiến dịch vụ không thể truy cập được.

Hãy cân nhắc kịch bản khi hạn mức API không được thực thi: một người dùng (thường là kẻ xâm nhập) có thể tải lên nhiều tệp tin dung lượng hàng GB cùng lúc hoặc thực hiện hàng nghìn yêu cầu mỗi giây. Những điểm cuối (endpoints) API như vậy sẽ dẫn đến việc tiêu thụ tài nguyên quá mức về mạng (network), lưu trữ (storage), tính toán (compute), v.v.

Ngày nay, những kẻ tấn công sử dụng các loại hình tấn công này để đảm bảo dịch vụ của một tổ chức không thể hoạt động, từ đó làm hoen ố danh tiếng thương hiệu do thời gian ngừng hoạt động (downtime) tăng cao. Một ví dụ đơn giản là việc không tuân thủ hệ thống Captcha trên biểu mẫu đăng nhập, cho phép bất kỳ ai cũng có thể thực hiện vô số truy vấn tới cơ sở dữ liệu thông qua một đoạn mã nhỏ viết bằng Python.

**Tác động tiềm tàng**

Cuộc tấn công này chủ yếu nhắm vào nguyên tắc Tính khả dụng (Availability) trong bảo mật; tuy nhiên, nó có thể làm tổn hại danh tiếng của thương hiệu và gây ra tổn thất về tài chính.

**Ví dụ thực tế (Practical Example)**

Tiếp tục sử dụng trình duyệt Chrome và Talend API Tester trên máy ảo (VM) để thực hành.

1. Kịch bản: Công ty MHT đã mua một gói tiếp thị qua email (20.000 email mỗi tháng) để gửi thông tin marketing, email khôi phục mật khẩu, v.v. Bob nhận ra rằng mình đã phát triển xong API đăng nhập, nhưng cần phải có thêm tùy chọn "Quên mật khẩu" để người dùng khôi phục tài khoản.

2. Thực hiện: Anh ấy bắt đầu xây dựng endpoint  /apirule4/sendOTP_v để gửi mã số gồm 4 chữ số tới địa chỉ email của người dùng. Người dùng sau đó sẽ sử dụng Mã xác thực một lần (OTP) đó để khôi phục tài khoản.

**Vấn đề ở đây là gì?**

Bob đã không bật bất kỳ giới hạn tần suất (rate limiting) nào cho endpoint này. Một kẻ xấu có thể viết một đoạn mã nhỏ và tấn công vét cạn (brute force) endpoint này, gửi hàng loạt email chỉ trong vài giây. Điều này sẽ tiêu tốn hết gói email mà công ty vừa mua, gây ra thiệt hại trực tiếp về tài chính.

**Giải pháp:**
Cuối cùng, Bob đã đưa ra một giải pháp thông minh hơn với endpoint /apirule4/sendOTP_s. Anh ấy đã kích hoạt tính năng giới hạn tần suất, yêu cầu người dùng phải đợi 2 phút mới có thể yêu cầu gửi lại mã OTP một lần nữa.

**Các biện pháp giảm thiểu (Mitigation Measures)**

- Sử dụng Captcha: Đảm bảo sử dụng captcha để tránh các yêu cầu từ các kịch bản tự động (scripts) và bot.

- Thiết lập hạn mức (Rate Limit): Đảm bảo triển khai giới hạn về tần suất một khách hàng có thể gọi API trong một khoảng thời gian nhất định và thông báo ngay lập tức khi vượt quá hạn mức.

- Giới hạn kích thước dữ liệu: Đảm bảo xác định kích thước dữ liệu tối đa cho tất cả các tham số và nội dung (payload), ví dụ: độ dài chuỗi tối đa và số lượng phần tử tối đa trong một mảng.


**Lỗi này xảy ra như thế nào?**

Lỗi phân quyền cấp chức năng (BFLA) phản ánh kịch bản trong đó một người dùng có đặc quyền thấp (ví dụ: nhân viên bán hàng) vượt qua các bước kiểm tra của hệ thống để truy cập vào dữ liệu bảo mật bằng cách mạo danh người dùng có đặc quyền cao (Quản trị viên - Admin).

Hãy cân nhắc một kịch bản với các chính sách kiểm soát truy cập phức tạp bao gồm nhiều phân cấp, vai trò và nhóm khác nhau. Nếu sự phân chia giữa các chức năng thông thường và chức năng quản trị không rõ ràng, nó sẽ dẫn đến những sai sót nghiêm trọng về ủy quyền. Tận dụng những vấn đề này, kẻ xâm nhập có thể dễ dàng truy cập vào các tài nguyên trái phép của người dùng khác hoặc nguy hiểm nhất là các chức năng quản trị.

BFLA cũng tương tự như lỗi quyền hạn IDOR, nơi người dùng (thường là kẻ xâm nhập) có thể thực hiện các tác vụ ở cấp độ quản trị. Các API có hệ thống vai trò người dùng phức tạp và quyền hạn trải dài trên nhiều cấp bậc thường dễ bị tấn công theo cách này hơn.

**Tác động tiềm tàng**

Cuộc tấn công này chủ yếu nhắm vào các nguyên tắc Ủy quyền (Authorization) và Chống thoái thác (Non-repudiation) trong bảo mật. Lỗi phân quyền cấp chức năng có thể dẫn đến việc kẻ xâm nhập mạo danh một người dùng hợp lệ và chiếm quyền quản trị để thực hiện các tác vụ nhạy cảm.

**Ví dụ thực tế (Practical Example)**
Tiếp tục sử dụng trình duyệt Chrome và Talend API Tester trên máy ảo (VM).

1. Kịch bản: Bob được giao nhiệm vụ phát triển một bảng điều khiển (dashboard) dành cho ban giám đốc công ty để họ có thể xem toàn bộ dữ liệu nhân viên và thực hiện các tác vụ cụ thể.

2. Cách triển khai của Bob: Bob xây dựng endpoint /apirule5/users_v để lấy dữ liệu của tất cả nhân viên. Để tăng cường bảo mật, anh ấy thêm một lớp bảo vệ bằng cách yêu cầu một tiêu đề (header) đặc biệt là isAdmin trong mỗi yêu cầu. API sẽ chỉ lấy thông tin nếu isAdmin=1 và mã Authorization-Token là chính xác.

3. Lỗ hổng: Mã token của Alice (một nhân viên nhân sự - không phải Admin) là YWxpY2U6dGVzdCFAISM6Nzg5Nzg=. Mặc dù Alice không phải là quản trị viên, nhưng cô ấy vẫn có thể xem toàn bộ dữ liệu nhân viên bằng cách tùy chỉnh yêu cầu gửi tới endpoint với giá trị isAdmin = 1.

**Vấn đề ở đây là gì?**

Hệ thống tin tưởng vào thông tin do phía máy khách (client) gửi lên (tiêu đề isAdmin) mà không kiểm tra lại vai trò thực sự của người dùng đó trong cơ sở dữ liệu.

**Giải pháp:**
Vấn đề này có thể được giải quyết bằng cách lập trình các quy tắc ủy quyền chính xác, kiểm tra vai trò chức năng của từng người dùng trong cơ sở dữ liệu ngay trong quá trình truy vấn. Bob đã triển khai một endpoint khác là /apirule5/users_s để xác thực vai trò của từng người dùng và chỉ hiển thị dữ liệu nếu vai trò thực sự là Admin.

**Các biện pháp giảm thiểu (Mitigation Measures)**

- Thiết kế và Kiểm thử: Đảm bảo thiết kế và kiểm thử kỹ lưỡng tất cả các hệ thống ủy quyền; áp dụng nguyên tắc Từ chối tất cả truy cập theo mặc định (Deny all access by default).

- Phân nhóm quyền hạn: Đảm bảo các hoạt động chỉ được phép thực hiện bởi những người dùng thuộc nhóm được ủy quyền tương ứng.

- Rà soát logic nghiệp vụ: Đảm bảo rà soát các endpoint API để tìm các lỗ hổng liên quan đến phân quyền cấp chức năng, đồng thời luôn ghi nhớ logic nghiệp vụ của ứng dụng và phân cấp nhóm người dùng.
