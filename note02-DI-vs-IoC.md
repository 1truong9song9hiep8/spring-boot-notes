# DI, IoC và IoC Spring Container
## Dependency Injection
Dependency Injection (DI) là một kỹ thuật trong lập trình để làm giảm sự phụ thuộc lẫn nhau giữa các lớp và đối tượng. Trong DI, các đối tượng con nằm bên trong một đối tượng cha không được tạo bên trong đối tượng đó, mà được cung cấp từ bên ngoài.

Có 3 phương thức để thực hiện ID:
- **Constructor Injection**: Dependency được tiêm vào thông qua constructor của lớp.
- **Setter Injection**: Dependency được tiêm vào thông qua các phương thức setter
- **Interface Injection**: Các lớp cần dependency phải implement một giao diện có phương thức để tiêm dependency.
```java
interface Runnable {
  public void run();
}
public class Person implement Runable {
  boolean canRun;

  Person(boolean canRun){
    this.canRun = canRun; // đây là kỹ thuật gán giá trị bằng Constructor Injection
  }

  @Override
  void run(){
    this.canRun = true; // đây là kỹ thuật gán giá trị bằng Interface Injection
  }

  void isCanRun(boolean canRun){
    this.canRun = canRun; // đây là kỹ thuật gán giá trị bằng Setter Injection
  }
}
```

## Inversion of Control
Inversion of Control (IoC) hay đảo ngược điều khiển, là một nguyên lý thiếu kế phần mềm trong đó luồng điều khiển của ứng dụng không được điều khiển bởi chương trình mà được điều khiển bởi framework hoặc container bên ngoài. Nguyên lý này chỉ để đảm bảo kế thừa từ DI.

## Spring IoC Container
Trong Spring framework, interface ApplicationContext đại diện cho IoC container. Spring container chịu trách nhiệm khởi tạo, cấu hình và lắp ráp các đối tượng được gọi là Beans cũng như quản lý vòng đời của chúng.
