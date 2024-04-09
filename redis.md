# Redis và Spring Boot
Redis là tên viết tắt của Remote Dictionary Server (Máy chủ từ điển từ xa)
Redis là 1 hệ thống lưu trữ key-value in-memory rất mạnh mẽ và phổ biến hiện nay. Tốc độ truy xuất dữ liệu cực kì nhanh chóng nên rất được dùng trong các ứng dụng hiện nay.


## 2. Cài đặt Redis
Tiến hành vào trang chủ và tải redis về sau đó cài đặt, nếu thành công, bạn có thể khởi động với lệnh: 
```
redis-server
```

## 3. Cấu hình Redis
Các client, bao gồm ứng dụng Spring Boot hoặc bất kỳ ứng dụng nào sử dụng Redis, sẽ kết nối đến Redis server thông qua một giao thức mạng như TCP/IP.
Chúng ta có thể kết nối bằng **Lettuce** hoặc **Jedis**.



Để sử dụng Redis trong Spring Boot, bạn chỉ cần khai báo dependency. Có thể


Cách thức hoạt động


Một cách ngắn gọn hơn là khai báo như dưới đây:
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
`spring-boot-starter-data-redis` chính là thư viện mà Spring cung cấp để ta có thể làm việc với Redis.

Trong file `application.properties` thêm các thông tin sau:
```
spring.redis.host=localhost
spring.redis.port=6379
```

Để kết nối ứng dụng với Redis server ta cần có một Redis client.


Tiếp theo tạo một file `RedisConfig` để cấu hình Redis trong Spring Boot:
```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.connection.RedisStandaloneConfiguration;
import org.springframework.data.redis.connection.lettuce.LettuceConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;

@Configuration
public class RedisConfig {
    @Value("${spring.redis.host}")
    private String redisHost;

    @Value("${spring.redis.port}")
    private int redisPort;

    @Bean
    public LettuceConnectionFactory redisConnectionFactory() {
        // Tạo Standalone Connection tới Redis
        return new LettuceConnectionFactory(new RedisStandaloneConfiguration(redisHost, redisPort));
    }

    @Bean
    @Primary
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        // Tạo một RedisTemplate
        // Với Key là Object
        // Value là Object
        // RedisTemplate giúp chúng ta thao tác với Redis
        RedisTemplate<Object, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }
}
```
