# Spring JPA và Hibernate
Spring JPA là một phần trong Spring Framework nó giúp kết nối và truy xuát dữ liệu từ database mà không cần code sql con mẹ gì hết.

ORM (Object-Relational Mapping) là kỹ thuật ánh xạ giữa đối tượng và cơ sở dữ liệu, giúp làm việc với cơ sở dữ liệu trừu tượng bằng cách sử dụng đối tượng Java thay vì truy vấn SQL trực tiếp.

Spring JPA (Java Persistence API) là một phần của Spring Framework và là một phần mở rộng của ORM. Nó cung cấp một cách dễ dàng để làm việc với cơ sở dữ liệu trong ứng dụng Spring. Spring JPA tự động tạo các truy vấn SQL dựa trên các phương thức trong các repository và cung cấp tích hợp mạnh mẽ với Hibernate - một trong các công cụ ORM phổ biến nhất trong cộng đồng Java.

Cài đặt 
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```
Tạo Entity Classe

Tạo Repository Interfaces


## Hinbernate
Hibernate là một framework được dùng nhiều, nó ánh xạ các class với các table
sử dụng chú thích của hibernate

seasion là một đối tượng quan trọng trong hibernate
