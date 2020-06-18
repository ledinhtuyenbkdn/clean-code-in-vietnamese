<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

# Chapter 2: Meaningful Names
- [Chapter 2: Meaningful Names](#chapter-2-meaningful-names)
  - [Giới thiệu](#gi%E1%BB%9Bi-thi%E1%BB%87u)
  - [Use Intention-Revealing Names(*Sử dụng tên bộc lộ ý định*)](#use-intention-revealing-namess%E1%BB%AD-d%E1%BB%A5ng-t%C3%AAn-b%E1%BB%99c-l%E1%BB%99-%C3%BD-%C4%91%E1%BB%8Bnh)
  - [Avoid Disinformation(*Tránh thông tin sai lệch*)](#avoid-disinformationtr%C3%A1nh-th%C3%B4ng-tin-sai-l%E1%BB%87ch)
  - [Make Meaningful Distinctions(*Tạo sự khác biệt có ý nghĩa*)](#make-meaningful-distinctionst%E1%BA%A1o-s%E1%BB%B1-kh%C3%A1c-bi%E1%BB%87t-c%C3%B3-%C3%BD-ngh%C4%A9a)
  - [Use Pronounceable Names(*Sử dụng tên có thể phát âm*)](#use-pronounceable-namess%E1%BB%AD-d%E1%BB%A5ng-t%C3%AAn-c%C3%B3-th%E1%BB%83-ph%C3%A1t-%C3%A2m)
  - [Use Searchable Names(*Sử dụng tên có thể tìm kiếm*)](#use-searchable-namess%E1%BB%AD-d%E1%BB%A5ng-t%C3%AAn-c%C3%B3-th%E1%BB%83-t%C3%ACm-ki%E1%BA%BFm)
  - [Avoid Encodings(*Tránh mã hóa*)](#avoid-encodingstr%C3%A1nh-m%C3%A3-h%C3%B3a)
    - [Hungarian Notation](#hungarian-notation)
    - [Member Prefixes](#member-prefixes)
    - [Interfaces and Implementations](#interfaces-and-implementations)
  - [Avoid Mental Mapping(*Tránh ánh xạ tinh thần*)](#avoid-mental-mappingtr%C3%A1nh-%C3%A1nh-x%E1%BA%A1-tinh-th%E1%BA%A7n)
  - [Class Names(*Tên lớp*)](#class-namest%C3%AAn-l%E1%BB%9Bp)
  - [Method Names(*Tên phương thức*)](#method-namest%C3%AAn-ph%C6%B0%C6%A1ng-th%E1%BB%A9c)
  - [Don’t Be Cute(*Đừng tỏ ra dễ thương*)](#dont-be-cute%C4%91%E1%BB%ABng-t%E1%BB%8F-ra-d%E1%BB%85-th%C6%B0%C6%A1ng)
  - [Pick One Word per Concept(*Chọn một từ cho mỗi khái niệm*)](#pick-one-word-per-conceptch%E1%BB%8Dn-m%E1%BB%99t-t%E1%BB%AB-cho-m%E1%BB%97i-kh%C3%A1i-ni%E1%BB%87m)
  - [Don’t Pun(*Đừng chơi chữ*)](#dont-pun%C4%91%E1%BB%ABng-ch%C6%A1i-ch%E1%BB%AF)
  - [Use Solution Domain Names(*Sử dụng tên miền giải pháp*)](#use-solution-domain-namess%E1%BB%AD-d%E1%BB%A5ng-t%C3%AAn-mi%E1%BB%81n-gi%E1%BA%A3i-ph%C3%A1p)
  - [Use Problem Domain Names(*Sử dụng tên miền nghiệp vụ*)](#use-problem-domain-namess%E1%BB%AD-d%E1%BB%A5ng-t%C3%AAn-mi%E1%BB%81n-nghi%E1%BB%87p-v%E1%BB%A5)
  - [Add Meaningful Context(*Thêm ngữ cảnh có nghĩa*)](#add-meaningful-contextth%C3%AAm-ng%E1%BB%AF-c%E1%BA%A3nh-c%C3%B3-ngh%C4%A9a)
  - [Don’t Add Gratuitous Context(*Đừng thêm bối cảnh vô cớ*)](#dont-add-gratuitous-context%C4%91%E1%BB%ABng-th%C3%AAm-b%E1%BB%91i-c%E1%BA%A3nh-v%C3%B4-c%E1%BB%9B)
  - [Final Words](#final-words)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

![Meaningfull names](./assets/images/chapter2.jpg)
## Giới thiệu
Tên được dùng khắp mọi nơi trong phần mềm. Chúng ta đặt tên cho biến, hàm, tham số, lớp và gói. Chúng ta cũng đặt tên cho tệp mã nguồn và thư mục chứa chúng.

Bởi vì chúng ta làm việc đặt tên rất nhiều lần, nên chúng ta cần làm tốt việc này.

Dưới đây là 1 vài nguyên tắc để có 1 cái tên tốt.
## Use Intention-Revealing Names(*Sử dụng tên bộc lộ ý định*)
Tên của 1 biến, hàm hay lớp nên trả lời tất cả những câu hỏi lớn. Nó nên nói cho bạn tại sao nó tồn tại, nó làm việc gì và nó được sử dụng như thế nào.

Nếu 1 cái tên đòi hỏi có thêm ghi chú, đó là 1 cái tên không bộc lộ được ý định.
**<p style="color: #f44336">Bad</p>**
```java
int d; // elapsed time in days
```
Biến `d` không làm gợi nhớ đến thứ gì cả. Ở ví dụ này, chúng ta cần 1 cái tên nói lên cái gì đang được đo và đơn vị của nó.
**<p style="color: #4caf50">Good</p>**
```java
int elapsedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;
```
Việc đặt tên bộc lộ ý định giúp dễ dàng hiểu, thay đổi mã nguồn hơn.

Nhiệm vụ của đoạn mã dưới đây là gì?
**<p style="color: #f44336">Bad</p>**
```java
public List<int[]> getThem() {
    List<int[]> list1 = new ArrayList<int[]>();
    for (int[] x : theList)
        if (x[0] == 4)
        list1.add(x);
    return list1;
}
```
Thật khó để hiểu đoạn mã nguồn đơn giản này làm việc gì? Vấn đề không nằm ở sự đơn giản của code mà ở sự ngầm định của code. Chúng ta cần phải biết:
- Các phần tử trong `theList` là loại gì?
- Vị trí `0` trong mỗi phần tử trong `theList` mang ý nghĩa gì?
- Giá trị `4` mang ý nghĩa gì?
- Danh sách trả về `list1` dùng để làm gì?
 
Câu trả lời cho những câu hỏi này không nằm sẳn trong đoạn code trên. Nhưng chúng ta có thể làm được điều đó.
**<p style="color: #4caf50">Good</p>**
```java
public List<Cell> getFlaggedCells() {
    List<Cell> flaggedCells = new ArrayList<Cell>();
    for (Cell cell : gameBoard)
        if (cell.isFlagged())
        flaggedCells.add(cell);
    return flaggedCells;
}
```
Với những thay đổi đơn giản trong cách đặt tên, đoạn code trở nên tường minh hơn, không còn quá khó khăn để hiểu.
## Avoid Disinformation(*Tránh thông tin sai lệch*)
Chúng ta cần tránh những từ mà ý nghĩa của nó đã ăn sâu vào suy nghĩ mọi người nhưng lại khác với ý nghĩa mong muốn hiện tại của mình.

Nếu muốn đặt tên cho 1 danh sách các `account` nên dùng `accountGroup`, `bunchOfAccounts` hay `accounts`, tránh đặt `accountList`(từ list mang ý nghĩa đặt trưng đối với lập trình viên, `accountList` có thể không phải kiểu `List`).

Cần tránh đặt những tên gần giống nhau như `XYZControllerForEfficientHandlingOfStrings`, `XYZControllerForEfficientStorageOfStrings`

Chú ý khi dùng chữ `l`(nhìn giống với số `1`), chữ O(nhìn giống với số `0`).
## Make Meaningful Distinctions(*Tạo sự khác biệt có ý nghĩa*)
Nếu những tên khác nhau, thì nó phải mang những ý nghĩa khác nhau.

Không nên dùng dãy số để đặt tên `a1, a2, ..., aN`. Vì nó không bộc lộ hết ý định của tác giả.
**<p style="color: #f44336">Bad</p>**
```java
 public static void copyChars(char a1[], char a2[]) {
    for (int i = 0; i < a1.length; i++) {
        a2[i] = a1[i];
    }
 }
```
**<p style="color: #4caf50">Good</p>**
```java
 public static void copyChars(char source[], char destination[]) {
    for (int i = 0; i < source.length; i++) {
        destination[i] = source[i];
    }
 }
```
Những noise words là dư thừa, chứa sự khác biệt vô nghĩa. 
Không nên đặt những tên gần giống nhau về nghĩa như `Product, ProductData, ProductInfo`
## Use Pronounceable Names(*Sử dụng tên có thể phát âm*)
Đoạn mã được phiên âm rõ ràng thì người sử dụng sẽ dễ dàng hiểu được vấn đề hơn.
**<p style="color: #f44336">Bad</p>**
```java
class DtaRcrd102 {
    private Date genymdhms;
    private Date modymdhms;
    private final String pszqint = "102";
    /* ... */
};
```
**<p style="color: #4caf50">Good</p>**
```java
class Customer {
    private Date generationTimestamp;
    private Date modificationTimestamp;;
    private final String recordId = "102";
    /* ... */
};
```
## Use Searchable Names(*Sử dụng tên có thể tìm kiếm*)
Tên biến chỉ gồm 1 chữ cái hay hằng số không dễ dàng để xác định vị trí.
Độ dài của tên phải tương ứng với kích thước scope của nó.

Nếu 1 biến hay hằng được sử dụng trong nhiều nơi trong code, thì tên của nó phải dễ dàng tìm kiếm được.
**<p style="color: #f44336">Bad</p>**
```java
for (int j=0; j<34; j++) {
    s += (t[j]*4)/5;
}
```
**<p style="color: #4caf50">Good</p>**
```java
int realDaysPerIdealDay = 4;
const int WORK_DAYS_PER_WEEK = 5;
int sum = 0;
for (int j=0; j < NUMBER_OF_TASKS; j++) {
    int realTaskDays = taskEstimate[j] * realDaysPerIdealDay;
    int realTaskWeeks = (realdays / WORK_DAYS_PER_WEEK);
    sum += realTaskWeeks;
}
```
## Avoid Encodings(*Tránh mã hóa*)
### Hungarian Notation
Không nên đặt tên biến có chứa kiểu dữ liệu trong nó.
**<p style="color: #f44336">Bad</p>**
```java
Long lAccountNum;
String phoneString;
```
### Member Prefixes
Không nên thêm tiền tố `m_` vào member variables. Những lớp và hàm phải đủ nhỏ để không cần đến chúng.
**<p style="color: #f44336">Bad</p>**
```java
public class Part {
    private String m_dsc; // The textual description
    void setName(String name) {
        m_dsc = name;
    }
}
```
**<p style="color: #4caf50">Good</p>**
```java
public class Part {
    String description;
    void setDescription(String description) {
        this.description = description;
    }
}
```
### Interfaces and Implementations
Nếu buộc phải mã hóa ở cách đặt tên của interface hay implementation class thì nên mã hóa ở tên của implementation class.
**<p style="color: #f44336">Bad</p>**
```java
public interface IShapeFactory {
    //...
}

public class ShapeFactory implements IShapeFactory {
    //...
}
```
**<p style="color: #4caf50">Good</p>**
```java
public interface ShapeFactory {
    //...
}

public class ShapeFactoryImpl implements ShapeFactory {
    //...
}
```
## Avoid Mental Mapping(*Tránh ánh xạ tinh thần*)
Một sự khác nhau giữa lập trình viên thông minh và lập trình viên chuyên nghiệp là lập trình viên chuyên nghiệp hiểu rằng: sự rõ ràng là vua("clarity is king"). Lập trình viên chuyên nghiệp dùng sức mạnh của mình để viết code cho những người khác có thể hiểu.

Thí dụ như trường hợp đặt tên biến bằng 1 chữ cái. Bộ đếm vòng lặp có thể đặt tên `i, j, k` nếu scope của nó rất nhỏ và không có sự xung đột với tên khác. Tuy nhiên, trong phần lớp trường hợp khác, đây là 1 lựa chọn tồi tệ. Nó bắt người đọc phải ánh xạ những tên biến này với khái niệm thực tế.
## Class Names(*Tên lớp*)
Tên lớp và đối tượng nên là danh từ, cụm danh từ như `Customer, WikiPage,
Account, AddressParser`.

Tránh đặt tên lớp có chứa các từ `Manager, Processor, Data, Info`.

Tên lớp không nên là động từ.
## Method Names(*Tên phương thức*)
Tên phương thức nên là động từ, cụm động từ như `postPayment, deletePage, save`.

Các phương thức truy cập, gán giá trị cho thuộc tính nên bắt đầu với tiền tố `set, get`.

Khi hàm dựng bị nạp chồng(overload), nên dùng static factory method với tên mô tả đối số
**<p style="color: #4caf50">Good</p>**
```java
Complex fulcrumPoint = Complex.FromRealNumber(23.0);
```
hơn là
**<p style="color: #f44336">Bad</p>**
```java
Complex fulcrumPoint = new Complex(23.0);
```
## Don’t Be Cute(*Đừng tỏ ra dễ thương*)
Không nên dùng từ lóng, khẩu ngữ hay teen code.

Ví dụ như: nên dùng `kill()` thay vì ` whack()`, nên dùng `abort` thay vì `eatMyShorts()`...
## Pick One Word per Concept(*Chọn một từ cho mỗi khái niệm*)
Nên chọn 1 từ cho mỗi khái niệm và gắn bó với nó.
**<p style="color: #f44336">Bad</p>**
```java
public interface UserService {
    User getById(Long id);
    User fetchByFullName(String fullName);
    List<User> findAll();
}
```
**<p style="color: #4caf50">Good</p>**
```java
public interface UserService {
    User findById(Long id);
    User findByFullName(String fullName);
    List<User> findAll();
}
```
## Don’t Pun(*Đừng chơi chữ*)
Tránh việc sử dụng cùng 1 từ cho 2 mục đích(2 nghĩa).
## Use Solution Domain Names(*Sử dụng tên miền giải pháp*)
Nhớ rằng những người đọc code của bạn cũng là những lập trình viên. Vì vậy, nên dùng những từ ngữ khoa học máy tính, tên thuật toán, tên mẫu thiết kế...

Ví dụ như: `ConnectionFactory, RecycleListAdapter`...
## Use Problem Domain Names(*Sử dụng tên miền nghiệp vụ*)
Khi làm việc với những thứ không phải của lập trình viên, nên dùng tên xuất phát từ miền nghiệp vụ. Ít nhất, người bảo trì code của bạn có thể hỏi các chuyên gia về nghiệp vụ tên đó có ý nghĩa gì.
## Add Meaningful Context(*Thêm ngữ cảnh có nghĩa*)
Những cái tên phần lớn không mang ý nghĩa nếu chúng không nằm trong 1 ngữ cảnh. Có thể thêm tiền tố nếu cần thiết. Nhưng tốt hơn hết là đặt chúng trong các lớp, hàm, namespace.

Ví dụ như: chúng ta có các biến `firstName, lastName, street, houseNumber, city, state, zipcode`. 

Chúng ta có thể thêm ngữ cảnh bằng cách sử dụng tiền tố: ` addrFirstName, addrLastName, addrState, ...`.

Giải pháp tốt hơn là tạo 1 lớp mang tên `Address`.
**<p style="color: #4caf50">Good</p>**
```java
public class Address {
    private String firstName;
    private String lastName;
    private String state;
    //...
}
```
## Don’t Add Gratuitous Context(*Đừng thêm bối cảnh vô cớ*)
Đừng thêm tiền tố cho tất cả lớp, phương thức để nói rằng chúng thuộc trong 1 ngữ cảnh riêng biệt.

Ví dụ như: chúng ta có ứng dụng `Image Sharing` -> `IMS`. Các lớp không nên đặt tên như: `IMSStudy, IMSUser, IMSMailTemplate`.
## Final Words
