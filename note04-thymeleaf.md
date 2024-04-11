# Thymeleaf
Thymeleaf là một thư viện mã nguồn mở hỗ trợ việc tạo và xử lý các trang HTML, XML, JavaScript, CSS...

Thymeleaf là một Java Template Engine tức là nó cho phép bạn kết hợp mã Java và giao diện người dùng một cách hiệu quả.

## Cài đặt Typeleaf trong Spring Boot
Việc cài đặt thymeleaf rất đơn giản, chỉ cần khai báo depencency trong file cấu hình là xong:
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```
sau đó tạo controller để xử lý và trả về tên file
```java
@GetMapping("/profile")
    public String profile(Model model) {
        // Tạo danh sách thông tin cá nhân
        List<Info> profile = new ArrayList<>();
        profile.add(new Info("fullname", "KungfuTech"));
        profile.add(new Info("nickname", "kungfutech"));
        profile.add(new Info("email", "techmely.creation@gmail.com"));
        profile.add(new Info("website", "https://kungfutech.me"));

        // Đưa danh sách vào Model
        model.addAttribute("kungfutechProfile", profile);

        // Trả về template profile.html
        return "profile";
    }
```
## Biến trong Thymeleaf
Chúng ta có thể truyền giá trị của một biến trong mã Java sang Thymeleaf bằng các biến.

th:object: cho phép bạn đặt một đối tượng vào template.
```html
<div th:object="${person}">
  <p>Name: <span th:text="*{name}"></span></p>
</div>
```
Ở đây, ${person} đại diện cho đối tượng Person, và bạn có thể truy cập thuộc tính name của nó bằng cách sử dụng *{name}.

Biến trường (th:field): thường sử dụng trong các form, nó đại diện cho một trường nhập dữ liệu.

### Hiển thị giá trị
trá trị biến với th:text
Gán giá trị vào trường th:value


### Điều kiện và Vòng lặp
Thymeleaf cũng hỗ trợ điều kiện và vòng lặp.
```html
<p th:if="${user.isAdmin()}">This user is an admin.</p>
<ul>
  <li th:each="item : ${items}" th:text="${item}"></li>
</ul>
```

## Standard Expression
Để lấy thông tin trong modal chúng ta sử dụng các cú pháp expression.
- ${...}: Lấy giá trị của một biến.
- *{...}: Lấy giá trị của một biến được chỉ định
- #{...}: Lấy message
- @{...}: Lấy đường dẫn URL dựa theo context của server

## Sử dụng Fragment
Khi sử dụng template có nhiều thứ mà chúng ta thường có những nội dung được dùng nhiều như header hay footer. chúng ta sẽ thường chi chúng ra rành các file khác nhau và gọi nó trong 1 file chính. vậy làm sao để file chính gọi được nội dung của các phần đó, chính là nhờ một thuộc tính `th:fragment`
```html
<!-- Trong header.html -->
<div th:fragment="header">
  <!-- Nội dung của header -->
</div>

<!-- Trong footer.html -->
<div th:fragment="footer">
  <!-- Nội dung của footer -->
</div>
```
và trong file chính ta chỉ cần :
```html
<!DOCTYPE html>
<html>
  <head>
    <title>Trang Chủ</title>
  </head>
  <body>
    <header th:replace="fragments/header :: header"></header>

    <div class="content">
      <!-- Nội dung của trang web -->
    </div>

    <footer th:replace="fragments/footer :: footer"></footer>
  </body>
</html>
```
