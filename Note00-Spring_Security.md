# NOTE 00: SPRING SECURITY

## Lời nói đầu
Lời nói đầu tôi muốn chửi vào mặt mấy thằng cha viết tài liệu của Spring Security bởi vì sau khi tôi đọc xong thì tôi chẳng hiểu con mẹ gì về Spring Security cả, tôi phải vừa đọc tài liệu của mấy ổng và cũng phải tìm thêm những bài viết của những chuyên gia bên ngoài mới có thể hiểu được đôi chút, để các bạn không gặp khó khăn như tôi khi mới bắt đầu làm quen với Spring Security thì tôi sẽ cố gắng làm một bài viết này để giúp các bạn rút ngắn đoạn đường, hy vọng bạn sẽ thích nó.

## Hello Spring Security
Chúng ta sẽ bắt đầu cài đặt Srping Security cho dự án Spring Boot của mình, rất đơn giản, bạn tạo một dự án spring boot, sau đó thêm 2 dependency sau:
```
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-security'
    ...
}
```
Lúc này, khi chạy thử ứng dụng, bạn sẽ thấy một vài thứ thú vị ở console:
```console
[           main] .s.s.UserDetailsServiceAutoConfiguration : Using generated security password: fb4edb34-0223-4c1f-8174-80a49b4ba846
[           main] o.s.s.web.DefaultSecurityFilterChain     : Will secure any request with
```
Và khi bạn request trên browser thì trang web localhost sẽ tự động chuyển hướng qua một trang login.

Khi bạn thêm spring-boot-starter-security vào dự án của mình, Spring Boot sẽ tự động cấu hình bảo mật bằng cách kích hoạt SecurityAutoConfiguration. SecurityAutoConfiguration là một trong những cấu hình chính được gọi đầu tiên khi ứng dụng khởi động.

Sau khi SecurityAutoConfiguration được khởi tạo, nó sẽ gọi các cấu hình bổ sung như DefaultSecurityFilterChain, UserDetailsServiceAutoConfiguration, và các cấu hình khác để cung cấp các cài đặt bảo mật mặc định cho ứng dụng của bạn. Điều này giúp đảm bảo rằng bảo mật được cấu hình và khởi tạo ngay từ khi ứng dụng bắt đầu chạy.

Tất cả những thứ trên được gọi là các cấu hình tự động của Spring Security. Khi Spring Security được cài đặt một số class của Spring Security được đánh dấu là @AutoConfiguration sẽ được khởi tạo và thực thi.


Cấu hình bảo mật mặc định được triển khai trong SecurityAutoConfiguration và trong các lớp được nhập từ đó (SpringBootWebSecurityConfiguration dành cho bảo mật web và AuthenticationManagerConfiguration dành cho cấu hình xác thực cũng có liên quan trong các ứng dụng không phải web). Để tắt hoàn toàn cấu hình bảo mật ứng dụng web mặc định, bạn có thể thêm một Bean bằng @EnableWebSecurity (điều này không vô hiệu hóa cấu hình trình quản lý xác thực hoặc bảo mật của Thiết bị truyền động). Để tùy chỉnh nó, bạn thường sử dụng các thuộc tính bên ngoài và các loại Bean thuộc loại WebSecurityConfigurerAdapter (ví dụ: để thêm thông tin đăng nhập dựa trên biểu mẫu).

Chúng ta có thể tắt cấu hình mặc định của Spring Security bằng cách loại bỏ việc khởi tạo SecurityAutoConfiguration khi khởi động ứng dụng bằng cách thêm `@SpringBootApplication(exclude = SecurityAutoConfiguration.class)`, hoặc cấu hình trong file cấu hình:
```
spring.autoconfigure.exclude=org.springframework.boot.autoconfigure.security.SecurityAutoConfiguration
```

Tuy nhiên,Thay vì loại bỏ cấu hình mặc định của Spring Security bằng cách sử dụng `exclude` trong `@SpringBootApplication`, một cách tiếp cận phổ biến và khuyến khích hơn là tạo một lớp mới và đánh dấu nó là `@EnableWebSecurity`. Khi làm như vậy, các cấu hình mặc định của Spring Security sẽ được tắt đi và được thay thế bằng các cấu hình do bạn định nghĩa trong lớp đó.

Việc này giúp giảm thiểu sự phụ thuộc vào các cấu hình mặc định của Spring và cung cấp sự linh hoạt lớn hơn cho việc tùy chỉnh bảo mật trong ứng dụng của bạn.

## Kiến Trúc 
Chúng ta đã biết về cách cài đặt Spring Boot cho ứng dụng và một số điểm quan trong về Spring Security như các cấu hình mặc định và các lớp quyết định những cấu hình đó, Spring Security hoạt động như một chuỗi các bộ lọc (filter chain), mỗi bộ lọc đảm nhận một nhiệm vụ cụ thể trong quá trình xử lý yêu cầu, nó có thể là một quá trình xác thực, quá trình phân quyền... Các yêu cầu sẽ lần lượt đi qua các bộ lọc này trước khi đến controller để xử lý.


Luồng cơ bản của Spring Security:

Đầu tiên, khi một client gửi một yêu cầu tới một ứng dụng web, Servlet container sẽ tạo một đối tượng `HttpServletRequest` để đại diện cho yêu cầu đó. `HttpServletRequest` cung cấp thông tin về yêu cầu như địa chỉ URL, phương thức HTTP, các headers, tham số, và các thông tin khác liên quan đến yêu cầu.

Tiếp theo, các đối tượng `HttpServletRequest` được điều hướng để đi qua một chuỗi các bộ lọc (filter chain) của Spring Security trước khi đến với `DispatcherServlet`.

DelegatingFilterProxy là một javax.servlet.Filter đặc biệt trong Spring Framework được sử dụng để giải quyết vấn đề của việc tích hợp các Filter của Spring vào trong môi trường Servlet.

Nhiệm vụ chính của DelegatingFilterProxy là tạo ra một bộ lọc (filter) trong Servlet container mà thực sự không thực hiện logic xử lý của nó, mà thay vào đó chuyển trách nhiệm cho một Filter được quản lý bởi Spring ApplicationContext.

Cụ thể, khi một yêu cầu đến và đi qua DelegatingFilterProxy, nó sẽ chuyển trách nhiệm xử lý yêu cầu cho một Filter Spring được định nghĩa trong Spring ApplicationContext, thường là một Filter được cấu hình trong cấu hình XML hoặc thông qua Java Configuration. Điều này giúp tích hợp Filter của Spring vào trong quy trình xử lý yêu cầu của Servlet container một cách dễ dàng và linh hoạt.


FilterChainProxy
Spring Security hỗ trợ Servlet thông qua FilterChainProxy:
Spring Security sử dụng FilterChainProxy để tích hợp bảo mật vào ứng dụng web.
FilterChainProxy là một bộ lọc đặc biệt được cung cấp bởi Spring Security, cho phép chuyển giao nhiều Filter thông qua SecurityFilterChain.
FilterChainProxy là một Bean, thường được bao bọc trong DelegatingFilterProxy:
FilterChainProxy được khai báo như một Bean trong Spring ApplicationContext, cho phép nó được quản lý và cấu hình bởi Spring.
DelegatingFilterProxy là một javax.servlet.Filter đặc biệt trong Spring, được sử dụng để tích hợp FilterChainProxy vào trong Servlet container.
Bằng cách này, Spring Security có thể quản lý và kích hoạt FilterChainProxy như một bộ lọc chuỗi thông qua DelegatingFilterProxy trong môi trường Servlet.

Có, đúng vậy. Trong môi trường Servlet, các chuỗi bộ lọc (filter chains) là một phần quan trọng của việc xử lý yêu cầu HTTP. Mỗi yêu cầu HTTP sẽ đi qua một chuỗi các bộ lọc trước khi đến được servlet hoặc các thành phần xử lý khác.

Spring Security sử dụng cơ chế chuỗi bộ lọc để quản lý các bộ lọc bảo mật. FilterChainProxy trong Spring Security là một ví dụ điển hình của một chuỗi bộ lọc, nó điều hướng các yêu cầu qua một loạt các SecurityFilterChain, mỗi SecurityFilterChain chứa một tập hợp các bộ lọc bảo mật.

Khi một yêu cầu HTTP đến, nó sẽ đi qua FilterChainProxy, sau đó được chuyển qua các SecurityFilterChain để xử lý bảo mật. Sau đó, nó sẽ tiếp tục đi qua các bộ lọc khác trong chuỗi bộ lọc của môi trường Servlet trước khi đến được servlet hoặc các thành phần xử lý khác.

SecurityFilterChain được FilterChainProxy



SecurityFilterChain là một interface có 2 method 
```java
public interface SecurityFilterChain {
  boolean matches(HttpServletRequest request);
  List<Filter> getFilters();
}
```
Trong SecurityFilterChain, hàm matches được sử dụng để kiểm tra xem một yêu cầu HTTP cụ thể có khớp với mẫu URL được cấu hình trong SecurityFilterChain hay không.

Khi một yêu cầu HTTP đến, FilterChainProxy sẽ lần lượt kiểm tra mỗi SecurityFilterChain để xác định xem yêu cầu đó thuộc về SecurityFilterChain nào. Để làm điều này, nó sử dụng hàm matches của mỗi SecurityFilterChain để kiểm tra xem URL của yêu cầu có khớp với mẫu URL được cấu hình trong SecurityFilterChain hay không.

Nếu URL của yêu cầu khớp với mẫu URL trong SecurityFilterChain, FilterChainProxy sẽ chuyển yêu cầu tới chuỗi các bộ lọc bảo mật trong SecurityFilterChain đó để xử lý. Nếu không có SecurityFilterChain nào khớp với URL của yêu cầu, yêu cầu sẽ được bỏ qua và không có bộ lọc bảo mật nào được áp dụng.

hàm getFilter() đơn giản nó sẽ trả về danh sách các Filter trong một FilterChain đó.

## Viết một Filter của riêng bạn
 Trong Servlet API, Filter là một interface được sử dụng để đại diện cho một bộ lọc (filter). Để tạo một bộ lọc (filter) tùy chỉnh trong ứng dụng của bạn, bạn chỉ cần triển khai (implement) interface này.

Khi bạn triển khai Filter, bạn cần cung cấp logic xử lý trong phương thức doFilter() của nó. Phương thức này sẽ được gọi mỗi khi một yêu cầu đi qua bộ lọc, cho phép bạn thực hiện các thao tác xử lý hoặc biến đổi yêu cầu hoặc phản hồi tùy thuộc vào nhu cầu của ứng dụng.
```
```
HttpSecurity là một bean được tạo từ lớp HttpSecurityConfiguration nằm trong package `org.springframework.security.config.annotation.web.configuration`

Như vậy, tổng kết lại để tùy chỉnh một chuỗi bộ lọc, ta cần viết 1 hàm trả về một SecurityFilterChain, nó sẽ nhận vào một HttpSecurity, trong hàm chúng ta tùy chỉnh các cấu hình và trả về:
```java
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(Customizer.withDefaults())
            .authorizeHttpRequests(authorize -> authorize
                .anyRequest().authenticated()
            )
            .httpBasic(Customizer.withDefaults())
            .formLogin(Customizer.withDefaults());
        return http.build();
    }
```

## Authentication
Xác thực (Authentication) là quá trình mà hệ thống xác định danh tính của người dùng, trả lời cho câu hỏi "Bạn là ai?"

Spring Security hỗ trợ một số cơ chế xác thực khác nhau như: xác thực bằng username/password, xác thực OAuth 2.0, SLMA 2.0, Remember Me...






