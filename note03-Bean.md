# BEAN
Bean là đối tượng được quản lý bởi Spring IoC (Inversion of Control) container. 

ApplicationContext là khái niệm Spring Boot dùng để chỉ Spring IoC container. Khi một ứng dụng Spring được khởi động thì đối tượng ApplicationContext luôn được khởi tạo đầu tiên.

Một bean có thể được tạo bằng cách đánh dấu các annotation `@Component`, `@Configuration`, `@Bean`, `@Service` trước các lớp hoặc phương thức.
