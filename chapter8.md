# Chapter 8: Boundaries

**Table of Contents**  

- [Chapter 8: Boundaries](#chapter-8-boundaries)
  - [1.1. Using Third-Party Code(*Sử dụng mã của bên thứ ba*)](#11-using-third-party-code)
  - [1.2. Exploring and Learning Boundaries(*Khám phá và học ranh giới*)](#12-exploring-and-learning-boundaries)
  - [1.3. Learning log4j(*Học log4j*)](#13-learning-log4j)
  - [1.4. Learning Tests Are Better Than Free(*Kiểm tra học tập tốt hơn miễn phí*)](#14-learning-tests-are-better-than-free)
  - [1.5. Using Code That Does Not Yet Exist(*Sử dụng mã nguồn chưa tồn tại*)](#15-using-code-that-does-not-yet-exist)
  - [1.6. Clean Boundaries(*Làm sạch ranh giới*)](#16-clean-boundaries)


- Chúng ta hiếm khi kiểm soát tất cả các phần mềm trong hệ thống của chúng ta. Thỉnh thoáng chúng ta mua `third-party packages`, hoặc sử dụng `open source`. Bằng cách nào đó chúng ta cần phải tích hợp source code từ bên ngoài này với source code của chúng ta. Trong chương này, chúng ta sẽ nhìn vào các bài thực hành và kỹ thuật để giữ cho `boundaries` của phần mềm của chúng ta được sạch sẽ.

## 1.1. Using Third-Party Code(*Sử dụng mã của bên thứ ba*)

- Các nhà cung cấp `third-party packages and frameworks` đang cố gắng cho khả năng ứng dụng rộng rãi để họ có thể làm việc trong nhiều môi trường và thu hút nhiều đối tượng.
- Mặt khác, Người dùng thì muốn một `interface` tập trung vào các nhu cầu cụ thể của họ.
- Sự căng thẳng này có thể gây ra vấn đề
tại ranh giới của hệ thống.
- Lấy `java.util.Map` làm ví dụ, `Map` có giao diện rất rộng với nhiều khả năng. Chắc chắn sức mạnh và tính linh hoạt này là hữu ích, nhưng nó cũng có thể là một trách nhiệm pháp lý. Ví dụ ứng dụng của ta có thể xây dựng `map` và `pass map around` (thêm đối tượng vào nó). Với mong muốn là không ai trong số những người nhận cái `map` xóa bất kì thứ gì trong đó.Tuy nhiên bất kì ai cũng có thể xóa được đó bởi vì `Map` có hàm `clear()`.

```java
clear() void – Map
containsKey(Object key) boolean – Map
containsValue(Object value) boolean – Map
entrySet() Set – Map
equals(Object o) boolean – Map
get(Object key) Object – Map
getClass() Class<? extends Object> – Object
hashCode() int – Map
isEmpty() boolean – Map
keySet() Set – Map
notify() void – Object
notifyAll() void – Object
put(Object key, Object value) Object – Map
putAll(Map t) void – Map
remove(Object key) Object – Map
size() int – Map
toString() String – Object
values() Collection – Map
wait() void – Object
wait(long timeout) void – Object
wait(long timeout, int nanos) void – Object
```

- 1 Ví dụ khác: Ta có một `Map` kiểu `Object` sau đó `casting` nó về `Sensor`

```java
Map sensors = new HashMap();
Sensor s = (Sensor)sensors.get(sensorId );
```

- Nó hoạt động, nhưng nó không sạch.
- Chúng ta có thể được cải thiện đáng kể bằng cách sử dụng `generic`.

```java
Map<Sensor> sensors = new HashMap<Sensor>(); 
Sensor s = sensors.get(sensorId );
```

- Tuy nhiên, `Passing an instance of Map<Sensor>` (vượt qua một thể hiện) một cách tự do trên hệ thống. Đồng nghĩa với việc chúng ta sửa chữa rất nhiều trên hệ thống nếu `Map` có thay đổi.
- Cải thiện code bằng cách bao bọc việc ép kiểu này trong hàm `getById` của lớp `Sensors`.

```java
public class Sensors {
    private Map sensors = new HashMap();
    
    public Sensor getById(String id) {
        return (Sensor) sensors.get(id);
    }
//snip
}
```

- Lúc này interface tại ranh giới `Map` bị ẩn. Nó có thể hoạt động với rất ít tác động đến phần còn lại của ứng dụng. 
Việc sử dụng generic không còn là vấn đề lớn vì việc ép kiểu được xử lý bên trong lớp Sensors.

{% hint style='info' %}

- Tác giả không đề xuất rằng mọi việc sử dụng `Map` sẽ được gói gọn trong trong một biểu mẫu.
- Thay vào đó tác giả khuyên bạn không nên `pass Maps (or any other interface at a boundary)` xung quanh hệ thống của bạn.
- Nếu bạn sử dụng giao diện ranh giới như Map, hãy giữ nó bên trong lớp hoặc gia đình gần gũi của các lớp, nơi nó được sử dụng.
- Tránh trả lại từ đó hoặc chấp nhận nó làm đối số cho API công khai.

{% endhint %}

## 1.2. Exploring and Learning Boundaries(*Khám phá và học ranh giới*)

- Mã của bên thứ ba giúp ta nhận được nhiều chức năng hơn trong thời gian ít hơn.
- Học mã của bên thứ ba là khó khăn. Việc tích hợp mã của bên thứ ba cũng khó. Làm cả hai cùng một lúc thì khó gấp đôi. Vì thế tác giả khuyên chúng ta nên tiếp cận chúng theo một cách mới.
- Thay vì thử nghiệm và thử các công cụ mới trong mã sản xuất, tác giả khuyên chúng ta nên viết một số bài kiểm tra để khám phá sự hiểu biết của mình về mã của bên thứ ba.
-`learning tests`: Viết các bài kiểm tra để khám phá sự hiểu biết của mình đối với `Third-Party Code`. Gọi API của bên thứ ba, Vì tác giả hi vọng chúng ta sẽ sử dụng nó trong ứng dụng của mình. Mục đích là để kiểm tra sự hiểu biết của mình về API đó.

## 1.3. Learning log4j(*Học log4j*)

- Hãy nói rằng chúng ta muốn sử dụng `log4j package` chứ không phải là `custom-built-logger`
- Chúng ta tải nó và mở trang tài liệu giới thiệu
- Viết trường hợp thử nghiệm đầu tiên, Hi vọng nó có thể  viết “hello” lên console được.

```java
@Test
	public void testLogCreate() {
		Logger logger = Logger.getLogger("MyLogger");
		logger.info("hello");
	}
```

- Khi run test case này thì xuất hiện 1 lỗi cho chúng ta biết chúng ta cần một thứ gọi là `Appender`.
- Sau khi đọc thêm tài liệu thì họ thấy rằng có một `ConsoleAppender`
- Tạo ra một `ConsoleAppender`.

```java
@Test
	public void testLogAddAppender() {
		Logger logger = Logger.getLogger("MyLogger");
		ConsoleAppender appender = new ConsoleAppender();
		logger.addAppender(appender);
		logger.info("hello");
	}
```

- Lần này thì họ thấy rằng Appender không có luồng đầu ra. 
- Thêm `PatternLayout`

```java
@Test
	public void testLogAddAppender() {
		Logger logger = Logger.getLogger("MyLogger");
		logger.removeAllAppenders();
		logger.addAppender(new ConsoleAppender(new PatternLayout("%p %t %m%n"), ConsoleAppender.SYSTEM_OUT));
		logger.info("hello");
	}
```

- Tác giả đã phát hiện ra rất nhiều về cách thức hoạt động của log4j và Tác giả đã mã hóa kiến thức đó thành một tập các bài kiểm tra đơn vị đơn giản.

```java
public class LogTest {
	private Logger logger;

	@Before
	public void initialize() {
		logger = Logger.getLogger("logger");
		logger.removeAllAppenders();
		Logger.getRootLogger().removeAllAppenders();
	}

	@Test
	public void basicLogger() {
		BasicConfigurator.configure();
		logger.info("basicLogger");
	}

	@Test
	public void addAppenderWithStream() {
		logger.addAppender(new ConsoleAppender(new PatternLayout("%p %t %m%n"), ConsoleAppender.SYSTEM_OUT));
		logger.info("addAppenderWithStream");
	}

	@Test
	public void addAppenderWithoutStream() {
		logger.addAppender(new ConsoleAppender(new PatternLayout("%p %t %m%n")));
		logger.info("addAppenderWithoutStream");
	}
}
```

{% hint style='info' %}
Bây giờ khi đã biết cách khởi tạo một trình ghi nhật ký  điều khiển đơn giản thì tác giả đã đóng gói kiến thức đó vào lớp `logger` của riêng mình để phần còn lại của ứng dụng của họ được tách biệt khỏi giao diện ranh giới `log4j`.
{% endhint %}

## 1.4. Learning Tests Are Better Than Free(*Kiểm tra học tập tốt hơn miễn phí*)

- Phải học API bằng mọi cách và viết các bài kiểm tra đó là một cách dễ dàng và tách biệt để có được kiến ​​thức đó.
- Các bài `learning tests` là những thí nghiệm chính xác giúp tăng hiểu biết của chúng ta về nó.
- Khi có các bản phát hành mới của gói bên thứ ba, chúng ta sẽ chạy các `learning tests` để xem liệu có sự khác biệt về hành vi so với phiên bản cũ trước đó hay không.
- Nếu không có các thử nghiệm ranh giới này để giảm bớt sự di chuyển, chúng tôi có thể bị cám dỗ ở lại với phiên bản cũ lâu hơn thay vì sử dụng phiên bản mới.

## 1.5. Using Code That Does Not Yet Exist(*Sử dụng mã nguồn chưa tồn tại*)

- Đôi khi, công việc cần thiết trong một mô-đun sẽ được kết nối với mô-đun khác đang được phát triển và chúng ta không biết làm thế nào để gửi thông tin, vì API chưa được thiết kế. Trong trường hợp này, nên tạo một interface để đóng gói giao tiếp với mô-đun đang chờ xử lý.
Bằng cách này, chúng ta duy trì quyền kiểm soát mô-đun của mình và chúng ta có thể kiểm tra mặc dù mô-đun thứ hai chưa khả dụng.

- Trong hình 8-2: Cách ly lớp `CommunicationsController` với `Transmitter API`( API này nằm ngoài tầm kiểm soát của ta và chưa được xác định). Bằng cách sử dụng `specific interface` cho ứng dụng của mình. Mục đích là giữ cho mã `CommunicationsController` được sạch sẽ và có hàm ý.
- Trong khi `Transmitter API` chưa được xác định ta viết `TransmitterAdapter` để thu hẹp khoảng cách.
![File Lenght](.\assets\images\chapter8_figure8-2.PNG)
- Thiết kế này cũng cho chúng ta một `seam` rất thuận tiện để kiểm tra.
- Sử dụng một `FakeTransmitter` phù hợp, chúng ta có thể kiểm tra các lớp `CommunicationsController`. Có thể tạo các thử nghiệm ranh giới khi có `Transmitter API` để đảm bảo rằng chúng ta đang sử dụng API chính xác.

## 1.6. Clean Boundaries(*Làm sạch ranh giới*)

- Có những điều thú vị xảy ra ở ranh giới.
- `Thay đổi` là một trong những điều đó.
- Thiết kế phần mềm tốt phù hợp với sự thay đổi mà không cần đầu tư lớn và làm lại.
- Mã tại các ranh giới cần phân tách rõ ràng và được kiểm tra xác định kỳ.
- Nên có rất ít vị trí trong mã của chúng ta đề cập đến mã của bên thứ 3, Tốt hơn là nên phụ thuộc vào những thứ mà ta <mark>kiểm soát được</mark> hơn những thứ ta <mark>không kiểm soát được</mark>. Vì vậy nên sử dụng các `wrapper` hoặc `adapter` để ranh giới luôn được sạch sẽ.