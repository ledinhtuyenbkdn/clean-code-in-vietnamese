
# Chapter 6 : Objects and Data Structures

**Table of Contents:**

- [Chapter 6 : Objects and Data Structures](#chapter-6--objects-and-data-structures)
  - [1. Data Abstraction](#1-data-abstraction)
  - [2. Data/Object Anti-Symmetry](#2-dataobject-anti-symmetry)
  - [3. The Law of Demeter](#3-the-law-of-demeter)
    - [Train Wrecks (Xác tàu bị đắm)](#train-wrecks-xác-tàu-bị-đắm)
    - [Hybrids](#hybrids)
  - [4. Data Transfer Objects](#4-data-transfer-objects)
    - [Active Record](#active-record)
  - [5. Conclusion](#5-conclusion)

## 1. Data Abstraction

- `Object` che dấu dữ liệu sau `abstractions`, và expose những method/function làm việc với những dữ liệu đó. `Data structures` expose data (variables) và không có những meaningful (significant) methods or functions.
- `Implementations` nên được ẩn đi, không chỉ bằng cách thêm một lớp functions giữa các biến. Chúng ta nên đưa ra những `abstractions` thích hợp. Không chỉ thao tác với các biến của class thông qua `getter và setter`, Các `abstract interfaces` nên được cung cấp để thao tác dữ liệu mà không cần biết `implementation`.

 Ví dụ: `Concrete` Point

 <span style="color:red;">**Bad**</span>

```java
public class Point {
  public double x;
  public double y;
}
```

-> Được triển khai rất rõ ràng trong `rectangular coordinates` và buộc chúng ta phải thao tác với các tọa độ đó một cách độc lập. -> Điều này phơi bày việc thực hiện ngay cả khi biến là private và sử dụng các getters and setters.

 Ví dụ: `Abstract` Point

 <span style="color:green">**Good**</span>

```java
public interface Point {
  double getX();
  double getY();
  void setCartesian(double x, double y);
  double getR();
  double getTheta();
  void setPolar(double r, double theta);
}
```

-> Không có cách nào biết việc triển khai là trong `rectangular or polar coordinates`

## 2. Data/Object Anti-Symmetry

Xem xét các ví dụ để thấy sự khác nhau giữa `Object` và `Data structures`.

Ví dụ 1: Procedural code sử dụng data structure.

```java
public class Square {
    public Point topLeft;
    public double side;
}
public class Rectangle {
     public Point topLeft;
     public double height;
     public double width;
}
public class Circle {
     public Point center;
     public double radius;
}
public class Geometry {
     public final double PI = 3.141592653589793;
     public double area(Object shape) throws NoSuchShapeException {
         if (shape instanceof Square) {
             Square s = (Square)shape;
             return s.side x s.side;
         }else if (shape instanceof Rectangle) {
             Rectangle r = (Rectangle)shape;
             return r.height x r.width;
         }else if (shape instanceof Circle) {
             Circle c = (Circle)shape;
             return PI x c.radius x c.radius; }
     throw new NoSuchShapeException(); }
 }
```

-> Trong ví dụ này, `shapes` là `data structures`, `Geometry class` là `Object`.

**Advantage:** nếu muốn thêm methods chúng ta chỉ cần thêm trong Geometry class.

**Disadvantage:** nếu muốn thêm nhiều Data Structures (e.g. more shapes) -> chúng ta phải thay đổi tất cả các method trong Geometry class.

Ví dụ 2: Object Oriented code sử dụng Object (using data abstraction, private variables).

```java
interface Shape {
  double area();
}

public class Square implements Shape {
  private Point topLeft;
  private double side;

  public double area() {
    return side x side;
  }
}

public class Rectangle implements Shape {
  private Point topLeft;
  private double height;
  private double width;

  public double area() {
    return height x width;
  }
}

public class Circle implements Shape {
  private Point center;
  private double radius;
  public final double PI = 3.141592653589793;

  public double area() {
    return PI x radius x radius;
  }
}
```

-> Trong ví dụ này, không có `data Structures` chỉ có `Object` classes.

**Advantage:** nếu muốn thêm nhiều shapes -> ta không phải thay đổi code cũ.

**Disadvantage:** nếu muốn thêm nhiều methods (trong Shape) -> ta phải thêm trong tất cả các class.

**=> Rút ra:**
> Procedural Code sử dụng `data structure` thì dễ dàng khi thêm new function mà không thay đổi data structure đã tồn tại. Mặt khác, `Object-Oriented` code thì dễ khi thêm new classes mà không thay đổi function đã tồn tại.

**Cũng như:**

> Code sử dụng `data structure` thì khó khăn khi thêm new data structures vì tất cả các function để phải thay đổi. Trong khi, `Object-Oriented` code khó khăn khi thêm new functions vì tất cả các class phải thay đổi.

**Kết luận:** Để viết một good code càng ít vấn đề phát sinh -> Tùy theo từng trường hợp, bạn phải quyết định chọn `procedural` hay `OO` một cách hợp lý.

## 3. The Law of Demeter

><mark>[Law of Demeter](http://en.wikipedia.org/wiki/Law_of_Demeter)</mark> nói rằng: Một module không nên biết về các bộ phận bên trong những đối tượng nó thao tác.

1 method `f` của 1 class `C` chỉ nên gọi những phương thức sau đây:

>- `C`
>- 1 object tạo ra bởi `f`.
>- 1 object được truyền dưới dạng 1 argument cho `f`.
>- 1 object được tổ chức trong 1 biến đối tượng của `C`.

-> Một method không nên gọi những method dựa trên đối tượng mà được trả về bởi các allowed function. <mark>"talk to friends, not to strangers"</mark>

Ví dụ: Vi phạm luật **Demeter** vì nó gọi hàm `getScratchDir()` dựa trên giá trị trả về của  `getOptions()` và sau đó gọi `getAbsolutePath()` trên giá trị trả về của `getScratchDir()`.

<span style="color:red">**Bad**</span>

```java
final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
```

### Train Wrecks (Xác tàu bị đắm)

![Train Wrecks](.\assets\images\chapter6.PNG)

Cách viết code này được gọi là `train wreck` vì nó giống như những toa tàu được nối chụm lại với nhau. -> <mark>đây là một kiểu cẩu thả, nên tránh</mark>.

<span style="color:green">**Good**</span>

```java
  Options opts = ctxt.getOptions();
  File scratchDir = opts.getScratchDir();
  final String outputDir = scratchDir.getAbsolutePath();
```

-> Nếu trong 1 phương thức mà bạn đang làm việc với `data structure` thì luật `demeter` không được áp dụng bởi vì bản chất tự nhiên của data structure là phơi bày cấu trúc bên trong chúng. Còn nếu bạn làm việc với 1 `Object` thì bạn nên cân nhắc việc kết nối những lời gọi phương thức vì chúng đang vi phạm luật `demeter`.

### **Hybrids**

<mark>Hybrids</mark> là 1 nữa `object` và 1 nữa `data structure`. Chúng có những function thực hiện những chức năng quan trọng, và chúng cũng có 1 trong 2 `public variables` hoặc `public accessors and mutators` -> **Hybrids** làm cho việc thêm new function cũng như  new data structure trở nên khó khăn. -> <mark>Nên tránh</mark>.

## 4. Data Transfer Objects

- <mark>DTO</mark> (Data Transfer Object) là một điều tinh túy của data `structure`. Đây là 1 class có những biến public và không có function.
- `DTOs` là một cấu trúc rất hữu ích, đặc biệt là khi giao tiếp với `database` hoặc phân tích messages từ socket,...
- Nó thường là công đoạn đầu tiên khi chuyển đổi từ dữ liệu thô trong database thành các đối tượng trong code.

Ví dụ: `address.java`

```java
public class Address {
    private String street;
    private String streetExtra;
    private String city;
    private String state;
    private String zip;

    public Address(String street, String streetExtra,
                    String city, String state, String zip) {
        this.street = street;
        this.streetExtra = streetExtra;
        this.city = city;
        this.state = state;
        this.zip = zip;
    }

    public String getStreet() {
        return street;
    }

    public String getStreetExtra() {
        return streetExtra;
    }

    public String getCity() {
        return city;
    }

    public String getState() {
        return state;
    }

    public String getZip() {
        return zip;
    }
}
```

### **Active Record**

- <mark>Active Record</mark> là một hình thức đặc biệt của `DTOs`.
- Nó là một `data structure` với public (or bean accessed) variables nhưng thường có những phương thức điều hướng như `save` và `find`. Những active record này chuyển đổi trực tiếp từ database tables hoặc data sources khác.
- Thật không may, ta thường thấy rằng các developers cố gắng xử lý những data structure này như thể nó là 1 object bằng cách thêm những business rule methods vào. -> Điều này là rất vụng về vì nó tạo ra một sự kết hợp giữa object và data structure.
- Giải pháp là coi những `active record` như 1 data structure và tạo ra những object riêng lẻ để chứa những business rules và che dấu những dữ liệu bên trong chúng.

## 5. Conclusion

- <mark>Object</mark> phơi bày hành vi, che dấu dữ liệu. -> Điều này là dễ dàng khi thêm object mới mà không phải thay đổi những hành vi đã tồn tại. Nhưng khó khăn hơn khi thêm hành vi mới vào những object đã tồn tại.

Ngược lại.

- <mark>Data structures</mark> phơi bày dữ liệu, không chứa những hành vi quan trọng nào. -> Dễ khi thêm hành vi trong những object đã tồn tại nhưng khó khăn khi thêm cấu trúc dữ liệu mới với những chức năng đã tồn tại.

=> Hiểu rõ những vấn đề này để đưa ra sự lựa chọn tốt nhất với task của bạn.
