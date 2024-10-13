# File Inclusion

> Tên Tài Liệu: Vulnlab

> Người Thực Hiện: Nguyễn Khánh Hào

> Cập Nhật Lần Cuối: 13/10/2024

# Mục Lục

[Summary](#summary)

[Learn the Capital 1](#learn-the-capital-1)

[Learn the Capital 2](#learn-the-capital-2)

[Learn the Capital 3](#learn-the-capital-3)

[Khuyến Nghị Khắc Phục](#khuyen-nghi-khac-phuc)

# Summary

File Inclusion là một loại lỗi cho phép attacker truy cập vào các tệp tin trên máy chủ web để đọc và thực thi chúng bằng cách lợi dụng hàm `include`.
Loại lỗi này có 2 biến thể được gọi là: `Local File Inclusion (LFI)` và `Remote File Inclusion (RFI)`.

* Local File Inclusion (LFI): lỗ hổng này nằm trong quá trình include file có sẵn trên server. Bằng cách sử dụng `../` thì attacker có thể đọc và truy cập tới những file nhạy cảm trên hệ thống.
* Remote File Inclusion (RFI): Tệp tin được nhúng đến từ một máy chủ từ xa, tức là attacker có thể chỉ định một URL đến tệp tin mà họ kiểm soát.

# Learn the Capital 1

![image](https://github.com/user-attachments/assets/ef9dd4c3-8307-4f8f-bd76-1498ea4a8cc7)

Sau khi access vào lab thì có thể thấy trang web cho chúng ta một chức năng xem thủ đô của từng quốc gia được chọn.

![image](https://github.com/user-attachments/assets/835bd023-29ef-447e-95fc-834505893c5d)

Sau khi xem qua source code thì có thể thấy untrusted data nằm ở biến `$page`, nó sẽ được gán giá trị cho biến `$_GET['country']`.

![image](https://github.com/user-attachments/assets/5de5a839-6cb6-401c-b48d-8779b71f0433)

Nếu giá trị của biến `$page` không được kiểm tra chặt chẽ thì attacker có thể hoàn toàn control được và lợi dụng để xem các file nhạy cảm hoặc thực thi code độc hại trên server.

Mục tiêu của lab này là truy cập vào trang của admin mà không hề có permission.

Final payload: `../admin.php`.

![image](https://github.com/user-attachments/assets/ff6aa644-fd55-4195-a0c6-23d873dcfdec)

# Learn the Capital 2

![image](https://github.com/user-attachments/assets/83e0620e-1d50-4159-a04e-711a823ce038)

![image](https://github.com/user-attachments/assets/927e0754-4a4c-4474-b08b-87676f24b079)

Sau khi ngó qua source code thì có thể thấy ở lab này anh Deverloper đã tiến hành replace `../` `..\` . Nhưng chúng ta thử nghỉ xem, nó chỉ replace 1 lần thôi =))) vậy câu hỏi đặt ra là nếu chúng ta nhét một payload như `....//` thì chuyện gì sẽ xảy ra ??

sau khi `../` được replace theo như đoạn code ban đầu của developer đặt ra thì có phải chúng ta vẫn còn thêm 2 dấu `..` và một dấu `/` phải không. Thế là bypass.

Final payload: `....//admin.php`

![image](https://github.com/user-attachments/assets/1047bca4-898c-4d11-9882-dfff289686f0)

# Learn the Capital 3

![image](https://github.com/user-attachments/assets/e9a000b6-fcc8-4732-9670-2f272ac210e9)

![image](https://github.com/user-attachments/assets/324d1207-aa32-4eb4-bf29-2ec9b221eb94)

Ở lab này developer đã sử dụng hàm `strstr()` để kiểm tra giá trị của biến `$page` có chứa từ file hay không.

Nếu không chứa "file", hệ thống sẽ in ra thông báo "ERROR: File not found!" và dừng thực thi mã bằng cách sử dụng `exit;`

Final payload để nhảy sang trang của admin: `file/../../admin.php`

![image](https://github.com/user-attachments/assets/de8f1ac4-6c7f-4132-9596-2860c1806f7a)

# Khuyến Nghị Khắc Phục

Lỗ hổng xảy ra khi việc kiểm tra đầu vào không được chú trọng. Khuyến cáo riêng thì không nên hoặc hạn chế tới mức tối thiểu phải sử dụng các biến từ “User Input” để đưa vào hàm include hay eval.  Trong trường hợp phải sử dụng. với các thông tin được nhập từ bên ngoài, trước khi đưa vào hàm cần được kiểm tra kỹ lưỡng.

    	
  1. Chỉ chấp nhận kí tự và số cho tên file (A-Z 0-9). Blacklist toàn bộ kí tự đặc biệt không được sử dụng.
     
  2. Giới hạn API cho phép việc include file từ một chỉ mục xác định nhằm tránh directory traversal.



