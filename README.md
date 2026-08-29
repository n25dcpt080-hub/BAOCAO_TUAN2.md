*PHÂN TÍCH UML CHO LỚP TRỪU TƯỢNG QUESTION CÓ HÀM VIRTUAL.


PHẦN 1: KHÁI NIỆM & MỤC TIÊU THIẾT KẾ


1.1. Khái niệm Lớp trừu tượng (Abstract Class) và Cơ chế ngăn khởi tạo


•	Lớp trừu tượng (Abstract Class): Là lớp được thiết kế để làm lớp cơ sở (Base Class) chung cho các lớp khác kế thừa. Lớp này chứa ít nhất một phương thức thuần ảo (Pure Virtual Function) và đóng vai trò như một bộ khung giao diện (Interface).


•	Vì sao không khởi tạo đối tượng Question trực tiếp?


o	Về mặt logic thực tế: Trong hệ thống làm bài thi, một câu hỏi xuất hiện trên đề bắt buộc phải thuộc một dạng cụ thể (như trắc nghiệm, điền từ, v.v.). Một "câu hỏi chung chung" không có hình thức hiển thị và cũng không có thuật toán chấm điểm cụ thể. Việc khởi tạo một đối tượng Question đơn lẻ là hoàn toàn vô nghĩa về mặt nghiệp vụ.


o	Về mặt kỹ thuật (C++): Lớp Question khai báo các hàm thuần ảo (có cú pháp = 0). Trình biên dịch sẽ tự động ngăn chặn mọi thao tác khởi tạo trực tiếp (ví dụ: Question q; sẽ gây lỗi biên dịch), từ đó đảm bảo tính an toàn và toàn vẹn cho cấu trúc phần mềm.


1.2. Mục tiêu thiết kế Lớp Question
Việc thiết kế Question làm lớp trừu tượng hướng tới 3 mục tiêu cốt lõi:


•	Tạo bộ khung định chuẩn chung (Standard Framework): Đóng gói toàn bộ các thuộc tính dùng chung (như mã câu hỏi, nội dung đề bài, số điểm) và định nghĩa các hành vi bắt buộc (hiển thị câu hỏi, chấm điểm) cho tất cả các loại câu hỏi trong hệ thống.


•	Chuẩn hóa giao diện lập trình: Tạo ra một "hợp đồng giao diện" (Interface Contract). Mọi thành viên phát triển các lớp con về sau đều bắt buộc phải tuân thủ việc cài đặt lại (Override) các hàm virtual theo đúng chuẩn đã định nghĩa ở lớp cha.


•	Tạo nền tảng cho Tính đa hình (Polymorphism): Cho phép hệ thống quản lý một đề thi chứa nhiều loại câu hỏi khác nhau thông qua một mảng hoặc danh sách các con trỏ lớp cha Question*. Nhờ đó, chương trình có thể duyệt qua danh sách đề thi và gọi các hàm hiển thị, chấm điểm một cách linh hoạt mà không cần quan tâm chi tiết kỹ thuật của từng lớp con.


PHẦN 2: BIỂU ĐỒ UML CỦA LỚP QUESTION Plaintext


```text
+------------------------------------------------------------+
|                        «abstract»                          |
|                         Question                           |
+------------------------------------------------------------+
| # id: string                                               |
| # content: string                                          |
| # score: double                                            |
+------------------------------------------------------------+
| + Question(id: string, content: string, score: double)     |
| + getId(): string                                          |
| + getContent(): string                                     |
| + getScore(): double                                       |
| + display(): void {abstract}                               |
| + checkAnswer(userAnswer: string): bool {abstract}         |
| + ~Question()                                              |
+------------------------------------------------------------+
```

PHẦN 3: PHÂN TÍCH CHI TIẾT CÁC THÀNH PHẦN LỚP QUESTION


3.1. Các Thuộc tính (Attributes)


Lớp Question định nghĩa 3 thuộc tính nền tảng với phạm vi truy cập protected (#):
•	# id: string: Mã định danh duy nhất cho từng câu hỏi trong hệ thống.
•	# content: string: Văn bản đề bài của câu hỏi.
•	# score: double: Trọng số điểm dành cho câu hỏi khi người dùng trả lời đúng.


Lý do lựa chọn phạm vi protected (#):
•	Đảm bảo tính đóng gói (Encapsulation): Ngăn chặn các đối tượng hoặc hàm nằm ngoài hệ thống truy cập hoặc chỉnh sửa trực tiếp dữ liệu của câu hỏi.


•	Tối ưu quyền truy cập cho Lớp con: Cho phép các lớp con kế thừa có thể đọc và thao tác trực tiếp trên các thuộc tính id, content, score mà không bắt buộc phải thông qua các hàm trung gian (Getter/Setter), giúp cú pháp mã nguồn ở các lớp con gọn gàng và đạt hiệu năng tốt hơn.


3.2. Các Phương thức chung (Common Methods)


1. Hàm khởi tạo (Constructor)

   
•	Cú pháp: Question(id: string, content: string, score: double)


•	Vai trò: Nhận dữ liệu khởi tạo và gán giá trị cho các thuộc tính dùng chung. Khi một lớp con được khởi tạo, constructor của lớp Question sẽ được gọi trước để hoàn tất phần dữ liệu nền tảng.


3. Hàm hủy ảo (Virtual Destructor)


Cú pháp: virtual ~Question()


Tầm quan trọng trong quản lý bộ nhớ:
Trong C++, khi hệ thống quản lý một câu hỏi cụ thể thông qua con trỏ của lớp cha (ví dụ: Question* q = new MultipleChoice()), việc giải phóng bộ nhớ bằng lệnh delete q; yêu cầu hàm hủy bắt buộc phải có từ khóa virtual.


•	Trường hợp KHÔNG sử dụng virtual: Trình biên dịch chỉ nhìn vào kiểu dữ liệu của con trỏ (Question*) và chỉ kích hoạt hàm hủy của lớp cha Question. Điều này làm cho toàn bộ phần dữ liệu riêng do lớp con MultipleChoice khởi tạo (như danh sách các đáp án A, B, C, D) bị bỏ qua, không được dọn dẹp. Dữ liệu rác này sẽ nằm lại trong RAM, gây ra sự cố Rò rỉ bộ nhớ (Memory Leak) nghiêm trọng khiến ứng dụng bị ngốn tài nguyên khi chạy lâu dài.


•	Trường hợp CÓ sử dụng virtual: Trình biên dịch sẽ áp dụng cơ chế đa hình để kiểm tra đối tượng thực sự bên trong. Chương trình sẽ tự động dọn dẹp theo đúng thứ tự: kích hoạt hàm hủy của lớp con (MultipleChoice) để giải phóng toàn bộ dữ liệu riêng trước, sau đó mới gọi hàm hủy của lớp cha (Question) để giải phóng khung cơ bản. Nhờ đó, toàn bộ bộ nhớ được dọn dẹp sạch sẽ 100%


3.3. Các Phương thức thuần ảo (Pure Virtual Functions)


Lớp Question định nghĩa 2 phương thức thuần ảo đóng vai trò cốt lõi:


•	virtual void display() = 0;: Hàm phụ trách hiển thị đề bài và phương án lựa chọn/ô nhập.
•	virtual bool checkAnswer(string userAnswer) = 0;: Hàm phụ trách nhận đáp án của người dùng, thực hiện logic so sánh và trả về kết quả Đúng (true) hoặc Sai (false).


Ý nghĩa của thiết kế "Bộ khung giao diện" (Interface Framework):


•	Ràng buộc trách nhiệm cài đặt: Việc gán cú pháp = 0 biến hàm thành thuần ảo. Đây là một "hợp đồng bắt buộc": Bất kỳ lớp con nào kế thừa từ Question bắt buộc phải tự viết mã nguồn xử lý riêng (Override) cho 2 hàm này nếu muốn khởi tạo đối tượng.


•	Tách biệt Thiết kế và Cài đặt: Lớp Question chỉ xác định CÁI GÌ cần có (mọi câu hỏi đều phải hiển thị được và chấm điểm được), còn việc làm điều đó NHƯ THẾ NÀO (in ra 4 đáp án hay in ra ô điền từ, chấm điểm bằng ký tự hay so sánh chuỗi) sẽ do từng lớp con tự quyết định.

