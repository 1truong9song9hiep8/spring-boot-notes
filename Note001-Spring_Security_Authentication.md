# NOTE 001: SPRING SECURITY - AUTHENTICATION
Ở note trước, chúng ta đã tìm hiểu cách cài đặt và có một cái nhìn tổng quan về luồng hoạt động của Spring Security. Khi một request được gửi đến, nó được đưa qua một chuỗi các bộ lọc bảo mật của Spring Security để kiểm tra tính hợp lệ của request và cuối cùng là chuyển tiếp request đến controller.

Các bộ lọc được đại diện bởi lớp `FilterChainProxy`, khi chạy cấu hình mặc định chúng ta thấy có một filter tên là `UsernamePasswordAuthenticationFilter` được kích hoạt, bên trong filter này sẽ có một hàm xử lý xác thực và sẽ trả về một đối tượng Authentication. Vậy chúng ta biết được cơ bản là quá trình xác thực sẽ nằm trong một filter, và khi xác thực xong một đối tượng Authentication chứa các thông tin vè người dùng sẽ được trả về. Tiếp theo chúng ta sẽ tìm hiểu một sô lớp và giao diện hỗ trợ quá trình xác thực, từ đó có thể thay đổi kiểu xác thực hoặc tự định nghĩa kiểu xác thực riêng cho ứng dụng.

Một request khi đi qua các filter ở tầng filter thứ 8 sẽ bị bắt lại bởi `UsernamePasswordAuthenticationFilter` filter này gọi là Authentication Filter, lúc này một hàm tiếp nhận request sẽ gọi đến Authentication manager, Authentication manager lại gửi yêu cầu đến Authentication provider, lúc này Authentication provider sẽ kiểm tra các thông tin trong request về thông tin của người gửi, các quyền của họ để xem thử là họ có đủ tiêu chuẩn để thõa mãn yêu cầu hày không? nếu có nó sẽ thông báo trở lại Authentication Manager và Authentication filter, đồng thời thông tin của user được lưu trong SecurityContext.

Tiếp theo ta đi sâu vào các class chịu trách nhiệm:

SecurityContextHolder là đối tượng dùng để lưu trữ những thông tin chi tiết của một chủ thể **đã được xác thực thành công**, Nếu như nó chứa dữ liệu thì Spring Security xem nó như là đã vượt qua thành công quá trình xác thực.


### SecurityContextHolder, SecurityContext và Athentication

SecurityContextHolder, SecurityContext và Athentication là 3 lớp có liên quan đến nhau, SecurityContextHolder có chứa một SecurityContext, và bên trong SecurityContext lại có một Athentication.

Authentication là một interface nó chứa các thông tin của chủ thể đã được xác thực.


Để trả về một Authentication từ các Athentication Filter, phải thông qua một method tên là `authenticate()` của một iterface khác là `AuthenticationManager`, sau đó đối tượng Authentication này sẽ được gán lại cho một đói tượng SecurityContext, 

ProviderManager thường được dùng để triển khai AuthenticationManager

