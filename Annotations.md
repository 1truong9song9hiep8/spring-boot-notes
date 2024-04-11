# Annotations
Annotation là những đánh dấu giúp bổ sung thông tin trong mã của bạn, nó giúp rút gọn mã nhưng trình biên dịch vẫn hiểu được các tính năng ngầm từ các annotation mang lại. 

Một số annotation thường gặp trong Java: @ovverride Nó được đặt trên một phương thức để thông báo rằng phương thức này ghi đè (override) một phương thức từ lớp cha. hay @SuppressWarnings sử dụng để tắt thông báo của trình biên dịch về class hoặc phương thức nào đó.

## Các Spring Annotation
Spring cung cấp một bộ các annotation rât hữu ích.
### `@Component` và `@Autowide`

`@Component` được dùng ở cấp độ lớp, nó đánh dấu class đó là một Bean và các instance của nó sẽ được quản lý bởi IoC container.

`@Autowired` được dùng ở cấp độ thuộc tính, các thuộc tính được đánh dấu bởi annotation này sẽ được khởi tạo tự động và tự động gắn vào.

### @Primary và @Qualifier
Một điều đặc biệt là các bean trong Spring Boot được quản lý bởi ApplicationContext đều là singleton. Điều này có nghĩa là chỉ có một instance duy nhất của mỗi bean và nó được chia sẻ giữa các thành phần khác nhau.

@Primary đánh dấu cho class đó là đối tượng được ưu tiên, khi IoC container chọn đối tượng gán thì nó sẽ được lưu ý đầu tiên


@Qualifier được dùng trong arugment, dùng để chỉ định chính xác tên mà instance được gán.

### @PostConstruct và @PreDestroy
`@PostConstruct` là một Annotation đánh dấu trên một method bên trong một Bean. IoC Container hoặc ApplicationContext sẽ gọi method này sau khi Bean đó được tạo ra và quản lý.

`@PreDestroy` là một Annotation đánh dấu trên một method bên trong một Bean. IoC Container hoặc ApplicationContext sẽ gọi method này trước khi Bean đó bị xóa hoặc không được quản lý nữa.

@PostConstruct và @PreDestroy rất hữu ích trong việc quản lý vòng đời của Bean. Bạn có thể sử dụng chúng để thực hiện các nhiệm vụ như cài đặt giá trị mặc định trong thuộc tính sau khi Bean được khởi tạo hoặc xóa dữ liệu trước khi Bean bị xóa.

### @Service và @Controller và @Repository

