# Spring Security P3: Authorization
Phân quyền (Authorization) là quá trình xác định những gì người dùng được phép thực hiện sau khi họ đã được xác thực. Nó trả lời câu hỏi "Bạn được phép làm gì?". thường sẽ là bước tiếp theo sau khi đã thực hiện xong sác thực.

Phân quyền trong Spring Security xảy ra ngay sau quá trình xác thực. Và các logic phân quyền được viết ở trong hàm xây dựng một `SecurityFilterChain` mà chúng ta đã hay thảo luận ở các chương trước.
```java
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http.authorizeHttpRequests((authz) -> authz
                        .requestMatchers("/greeting/**").hasRole("USERqưdqw3qw3d")
                        .anyRequest().authenticated()
                ).formLogin(form -> form.loginPage("/login"));
        return http.build();
    }
```
Ở hàm trên, chúng ta để ý rằng có hàm `authorizeHttpRequests(...)`, hàm này định nghĩa các logic phân quyền cho request gửi đến, bên trong hàm này là một lambda để định nghĩa các logic phan quyền:
Phương thức requestMatchers(String) trong Spring Security có nhiệm vụ chỉ định các mẫu đường dẫn mà các cấu hình bảo mật nên áp dụng. Tuy nhiên, khi sử dụng một mình, nó không thực hiện bất kỳ hành động gì nếu không kết hợp với các phương thức cấu hình khác như: `permitAll()` (cho phép mọi user), `hasRole(String role)` (chỉ định user có role nào đó mới dc vào), 

Các logic phân quyền được viết dựa vào các hàm cung cấp sẵn trong spring boot

hasRole(String role): Xác định rằng người dùng cần phải có một vai trò cụ thể để truy cập vào tài nguyên.
hasAnyRole(String... roles): Xác định rằng người dùng cần phải có ít nhất một trong các vai trò được chỉ định để truy cập vào tài nguyên.
hasAuthority(String authority): Tương tự như hasRole, nhưng sử dụng cho các quyền được chỉ định bằng tên (authority).
hasAnyAuthority(String... authorities): Tương tự như hasAnyRole, nhưng sử dụng cho các quyền được chỉ định bằng tên (authority).
permitAll(): Cho phép tất cả mọi người truy cập vào tài nguyên mà không cần xác thực.
authenticated(): Yêu cầu người dùng phải được xác thực để truy cập vào tài nguyên.
denyAll(): Từ chối tất cả các yêu cầu truy cập.
anonymous(): Cho phép truy cập từ người dùng ẩn danh (không xác thực).
rememberMe(): Cho phép truy cập từ người dùng đã đăng nhập bằng tính năng "ghi nhớ đăng nhập".

## Method Security
Ngoài việc ủy ​​quyền mô hình hóa ở cấp độ yêu cầu , Spring Security còn hỗ trợ mô hình hóa ở cấp độ phương thức.

Bạn có thể kích hoạt nó trong ứng dụng của mình bằng cách chú thích bất kỳ @Configurationlớp nào bằng @EnableMethodSecurity hoặc thêm <method-security>vào bất kỳ tệp cấu hình XML nào, như sau:
```java
@EnableMethodSecurity
```

Sau đó, bạn có thể chú thích ngay lập tức bất kỳ lớp hoặc phương thức nào do Spring quản lý bằng @PreAuthorize, @PostAuthorize, @PreFiltervà @PostFiltercho phép gọi phương thức, bao gồm các tham số đầu vào và giá trị trả về. (Chú ý: Mặc định Spring Security không tự kích hoạt Method Security)

Method Security thường được khuyến khích sử dụng.



Một so sánh về phân quyền ở cấp độ request và cấp độ method:

loại ủy quyền:

nơi cấu hình: (request: ở file config), method: ở từng method

phong cách cấu hình: request: DSL, method: annotation

khi phân quyền ở cấp độ phương thức thì chỉ có method nào được đánh dấu annotation mơi được 

### Các annotation phân quyền cấp độ phương thức
@PreAuthorization
```java
@Component
public class BankService {
	@PreAuthorize("hasRole('ADMIN')")
	public Account readAccount(Long id) {
        // ... is only invoked if the `Authentication` has the `ROLE_ADMIN` authority
	}
}
```
**!Note:** `@PreAuthorize` có thể được dùng trước một interface hoặc một class để áp dụng phân quyền cho toàn bộ các hàm bên trong nó. `@PreAuthorize` cũng có thể là một metadata như dưới đây:
```java
@Target({ ElementType.METHOD, ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)
@PreAuthorize("hasRole('ADMIN')")
public @interface IsAdmin {}
```
### Phương thức ủy quyền Kết quả với@PostAuthorize
Khi Bảo mật Phương thức được kích hoạt, bạn có thể chú thích một phương thức với @PostAuthorizechú thích như sau:
```java
@Component
public class BankService {
	@PostAuthorize("returnObject.owner == authentication.name")
	public Account readAccount(Long id) {
        // ... is only returned if the `Account` belongs to the logged in user
	}
}
```
Khi một phương thức được gắn với @PostAuthorize, kết quả của phương thức đó sẽ được kiểm tra sau khi phương thức đã được thực thi, và nếu kết quả không thỏa mãn điều kiện xác định, Spring Security sẽ ném một ngoại lệ AccessDeniedException.

**!Note:** `@PreAuthorize` có thể được dùng trước một interface hoặc một class để áp dụng phân quyền cho toàn bộ các hàm bên trong nó. `@PreAuthorize` cũng có thể là một metadata như dưới đây:
```java
@Target({ ElementType.METHOD, ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)
@PostAuthorize("returnObject.owner == authentication.name")
public @interface RequireOwnership {}
```
Kết quả là phương thức trên sẽ chỉ trả về Accountnếu ownerthuộc tính của nó khớp với thuộc tính của người dùng đã đăng nhập name. Nếu không, Spring Security sẽ gửi AccessDeniedExceptionvà trả về mã trạng thái 403.

### Lọc các thông số phương pháp với@PreFilter
@PreFilterchưa được hỗ trợ cho các loại dữ liệu dành riêng cho Kotlin; vì lý do đó, chỉ các đoạn mã Java được hiển thị

```java
@Component
public class BankService {
	@PreFilter("filterObject.owner == authentication.name")
	public Collection<Account> updateAccounts(Account... accounts) {
        // ... `accounts` will only contain the accounts owned by the logged-in user
        return updated;
	}
}
```
Khi một phương thức được chú thích với @PreFilter, danh sách các đối tượng sẽ được lọc trước khi phương thức được gọi. Các đối tượng không thỏa mãn điều kiện được xác định trong @PreFilter sẽ được loại bỏ khỏi danh sách trước khi chúng được truyền vào phương thức.

`@PreFilter` có thể áp dụng cho mảng, collcection và steam (miễn là nó chưa đóng). Ví dụ: updateAccountskhai báo trên sẽ hoạt động tương tự như bốn khai báo còn lại sau:
```java
@PreFilter("filterObject.owner == authentication.name")
public Collection<Account> updateAccounts(Account[] accounts)

@PreFilter("filterObject.owner == authentication.name")
public Collection<Account> updateAccounts(Collection<Account> accounts)

@PreFilter("filterObject.value.owner == authentication.name")
public Collection<Account> updateAccounts(Map<String, Account> accounts)

@PreFilter("filterObject.owner == authentication.name")
public Collection<Account> updateAccounts(Stream<Account> accounts)
```
### Phương pháp lọc kết quả với@PostFilter
`@PostFilter` được sử dụng để lọc danh sách kết quả sau khi phương thức đã được thực thi.
```java
```


### @Secured trong Spring Security được sử dụng để xác định quyền truy cập cần thiết cho một phương thức hoặc lớp dịch vụ. Nó cho phép bạn chỉ định các quyền mà người dùng cần phải có để truy cập vào phương thức hoặc lớp đó.




### Cho phép gọi hàm với Spring JSR-250 Annotations
Trong trường hợp bạn muốn sử dụng chú thích JSR-250 , Spring Security cũng hỗ trợ điều đó. @PreAuthorizecó sức mạnh biểu đạt cao hơn và do đó được khuyến khích.

Để sử dụng các chú thích JSR-250, trước tiên bạn nên thay đổi khai báo Bảo mật Phương thức để kích hoạt chúng như sau:
```java
@EnableMethodSecurity(jsr250Enabled = true)
```
Khi kích hoạt 
```
Điều này sẽ khiến Spring Security xuất bản trình chặn chặn phương thức tương ứng cho phép các phương thức, lớp và giao diện được chú thích bằng @RolesAllowed, @PermitAll, và @DenyAll.

### Khai báo chú thích ở cấp độ lớp hoặc giao diện



