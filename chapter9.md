# Chapter 9: Unit Tests

**Table of Contents**  

- [Chapter 9: Unit Tests](#chapter-9-unit-tests)
  - [1.1. The Three Laws of TDD](#11-the-three-laws-of-tdd)
    - [1.1.1. Why use TDD?](#111-why-use-tdd?)
    - [1.1.2. Rules](#112-rules)
  - [1.2. Keeping Tests Clean](#12-keeping-tests-clean)
    - [1.2.1. Tests Enable the -ilities](#121-tests-enable-the--ilities)
  - [1.3. Clean Tests](#13-clean-tests)
    - [1.3.1. Domain-Specific Testing Language](#131-domain-specific-testing-language)
    - [1.3.2. A Dual Standard](#132-a-dual-standard)
  - [1.4. One Assert per Test](#14-one-assert-per-test)
  - [1.5. F.I.R.S.T](#15-f.i.r.s.t)
  - [1.6. Conclusion](#16-conclusion)

## 1.1. The Three Laws of TDD

### 1.1.1. Why use TDD?

- Vì đây là cách đơn giản nhất để đạt được cả mã chất lượng tốt và phạm vi kiểm tra tốt.

### 1.1.2. Rules

- Đến bây giờ mọi người đều biết rằng TDD yêu cầu chúng ta viết unit test trước khi chúng ta viết code. Nhưng quy tắc đó chỉ là phần nổi của tảng băng chìm. Hãy xem xét ba luật sau đây: 
- `First Law`: You may not write production code until you have written a failing unit test.
=> Không cho phép viết bất kỳ một mã chương trình nào cho tới khi nó làm một test case bị fail trở nên pass.
- `Second Law`: You may not write more of a unit test than is sufficient to fail, and not compiling is failing.
=> Không cho phép viết nhiều hơn một unit test mà nếu chỉ cần 1 unit test cũng đã đủ để fail. Hãy chuyển sang viết code function để pass test đó trước.
- `Third Law`: You may not write more production code than is sufficient to pass the currently failing test
=> Không cho phép viết nhiều hơn 1 mã chương trình mà nó đã đủ làm một test bị fail chuyển sang pass

## 1.2. Keeping Tests Clean

- Mã kiểm tra cũng quan trọng như mã sản xuất.
- Nó đòi hỏi suy nghĩ, thiết kế và chăm sóc.
- Nó phải được giữ sạch như mã sản xuất.

### 1.2.1. Tests Enable the -ilities

- Đây là các `thử nghiệm` đơn vị giữ cho mã của chúng ta linh hoạt, có thể duy trì và có thể sử dụng lại bởi vì nếu bạn có các `thử nghiệm`, bạn không sợ thực hiện các thay đổi đối với mã. Không có kiểm tra, mọi thay đổi là một lỗi có thể xảy ra.
- Cho dù kiến ​​trúc của bạn có dễ hiểu đến đâu, cho dù thiết kế của bạn được phân chia độc đáo như thế nào, không có kiểm tra, bạn sẽ miễn cưỡng thực hiện các thay đổi vì sợ rằng bạn sẽ đưa ra các lỗi không bị phát hiện. Nhưng với các bài kiểm tra nỗi sợ hãi hầu như biến mất. Phạm vi kiểm tra của bạn càng cao, nỗi sợ hãi của bạn càng ít.

## 1.3. Clean Tests

- `Clean Test`: <mark>Readability, readability, and readability </mark>. Khả năng đọc có lẽ thậm chí còn quan trọng hơn trong các bài kiểm tra đơn vị so với trong mã sản xuất.
- `Readable code`: <mark>sự rõ ràng, đơn giản và mật độ của biểu thứ </mark>. Bạn muốn nói thật nhiều với càng ít biểu cảm càng tốt.

- Xem đoạn ví dụ bên dưới ta thấy: có một số lượng lớn mã trùng lặp trong các lệnh gọi lặp lại tới `addPage` và `assertSubString`.

```java
public void testGetPageHieratchyAsXml() throws Exception {
		crawler.addPage(root, PathParser.parse("PageOne"));
		crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
		crawler.addPage(root, PathParser.parse("PageTwo"));
		request.setResource("root");
		request.addInput("type", "pages");
		Responder responder = new SerializedPageResponder();
		SimpleResponse response = (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
		String xml = response.getContent();
		assertEquals("text/xml", response.getContentType());
		assertSubString("<name>PageOne</name>", xml);
		assertSubString("<name>PageTwo</name>", xml);
		assertSubString("<name>ChildOne</name>", xml);
	}

	public void testGetPageHieratchyAsXmlDoesntContainSymbolicLinks() throws Exception {
		WikiPage pageOne = crawler.addPage(root, PathParser.parse("PageOne"));
		crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
		crawler.addPage(root, PathParser.parse("PageTwo"));
		PageData data = pageOne.getData();
		WikiPageProperties properties = data.getProperties();
		WikiPageProperty symLinks = properties.set(SymbolicPage.PROPERTY_NAME);
		symLinks.set("SymPage", "PageTwo");
		pageOne.commit(data);
		request.setResource("root");
		request.addInput("type", "pages");
		Responder responder = new SerializedPageResponder();
		SimpleResponse response = (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
		String xml = response.getContent();
		assertEquals("text/xml", response.getContentType());
		assertSubString("<name>PageOne</name>", xml);
		assertSubString("<name>PageTwo</name>", xml);
		assertSubString("<name>ChildOne</name>", xml);
		assertNotSubString("SymPage", xml);
	}

	public void testGetDataAsHtml() throws Exception {
		crawler.addPage(root, PathParser.parse("TestPageOne"), "test page");
		request.setResource("TestPageOne");
		request.addInput("type", "data");
		Responder responder = new SerializedPageResponder();
		SimpleResponse response = (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
		String xml = response.getContent();
		assertEquals("text/xml", response.getContentType());
		assertSubString("test page", xml);
		assertSubString("<Test", xml);
	}
```

- Bây giờ hãy xem xét các thử nghiệm được cải thiện

```java
public void testGetPageHierarchyAsXml() throws Exception {
		makePages("PageOne", "PageOne.ChildOne", "PageTwo");
		submitRequest("root", "type:pages");
		assertResponseIsXML();
		assertResponseContains("<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
	}

	public void testSymbolicLinksAreNotInXmlPageHierarchy() throws Exception {
		WikiPage page = makePage("PageOne");
		makePages("PageOne.ChildOne", "PageTwo");
		addLinkTo(page, "PageTwo", "SymPage");
		submitRequest("root", "type:pages");
		assertResponseIsXML();
		assertResponseContains("<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
		assertResponseDoesNotContain("SymPage");
	}

	public void testGetDataAsXml() throws Exception {
		makePageWithContent("TestPageOne", "test page");
		submitRequest("TestPageOne", "type:data");
		assertResponseIsXML();
		assertResponseContains("test page", "<Test");
	}
```

- `BUILD-OPERATE-CHECK pattern` được thể hiện rõ ràng bằng cấu trúc của các thử nghiệm này. Mỗi bài kiểm tra rõ ràng được chia thành ba phần. Phần đầu tiên xây dựng dữ liệu thử nghiệm, phần thứ hai hoạt động trên dữ liệu thử nghiệm đó và phần thứ ba kiểm tra xem hoạt động mang lại kết quả như mong đợi.

### 1.3.1. Domain-Specific Testing Language

- Thay vì sử dụng các API mà các lập trình viên sử dụng để thao tác hệ thống, tác giả xây dựng một bộ các chức năng và tiện ích sử dụng các API đó và giúp các bài kiểm tra dễ viết và dễ đọc hơn. Các chức năng và tiện ích này trở thành một API chuyên dụng được sử dụng bởi các bài kiểm tra.
- Chúng là một `Testing language` mà các lập trình viên sử dụng để giúp bản thân họ viết các bài kiểm tra của họ và để giúp những người phải đọc các bài kiểm tra đó sau này. API thử nghiệm này không được thiết kế trước; thay vào đó, nó phát triển từ việc tiếp tục tái cấu trúc mã kiểm tra đã bị quá mờ bởi chi tiết che giấu.

### 1.3.2. A Dual Standard

- Ở một khía cạnh nào đó, `code within the testing API` có một bộ tiêu chuẩn kỹ thuật khác với `production code`. Nó vẫn phải đơn giản, cô đọng và có hàm ý, nhưng nó không cần hiệu quả như `production code`. Sau tất cả thì nó chạy trong một môi trường thử nghiệm, không phải môi trường sản xuất và hai môi trường đó có những nhu cầu rất khác nhau.
- Xét ví dụ về bài test kiểm tra nhiệt độ

```java
class EnvironmentControllerTest {
	@Test
	public void turnOnLoTempAlarmAtThreashold() throws Exception {
		hw.setTemp(WAY_TOO_COLD);
		controller.tic();
		assertTrue(hw.heaterState());
		assertTrue(hw.blowerState());
		assertFalse(hw.coolerState());
		assertFalse(hw.hiTempAlarm());
		assertTrue(hw.loTempAlarm());
	}
}
```

- Ở đây cải thiện bằng cách bỏ function `tic()` và thêm function `wayTooCold()`.
```java
class EnvironmentControllerTest {
	@Test
	public void turnOnLoTempAlarmAtThreshold() throws Exception {
		wayTooCold();
		assertEquals("HBchL", hw.getState());
	}
}
```

- Tuy nhiên có 1 chuỗi lạ trong `assertEquals`. Kí tự viết hoa có nghĩa là trạng thái `on`, kí tự viết thường có nghĩa là trạng thái `off`. Và ý nghĩa của các kí tự viết tắt lần lượt là như sau: `{heater, blower, cooler,
hi-temp-alarm, lo-temp-alarm}.`

```java
class EnvironmentControllerTest {
	@Test
	public void turnOnCoolerAndBlowerIfTooHot() throws Exception {
		tooHot();
		assertEquals("hBChl", hw.getState());
	}

	@Test
	public void turnOnHeaterAndBlowerIfTooCold() throws Exception {
		tooCold();
		assertEquals("HBchl", hw.getState());
	}

	@Test
	public void turnOnHiTempAlarmAtThreshold() throws Exception {
		wayTooHot();
		assertEquals("hBCHl", hw.getState());
	}

	@Test
	public void turnOnLoTempAlarmAtThreshold() throws Exception {
		wayTooCold();
		assertEquals("HBchL", hw.getState());
	}
}
```

- Hàm `getState()`
```java
class MockControlHardware {
	public String getState() {
		String state = "";
		state += heater ? "H" : "h";
		state += blower ? "B" : "b";
		state += cooler ? "C" : "c";
		state += hiTempAlarm ? "H" : "h";
		state += loTempAlarm ? "L" : "l";
		return state;
	}
}
```

{% hint style='info' %}
Đó là bản chất của `Dual standard`(tiêu chuẩn kép). Có những điều bạn có thể không bao giờ làm trong môi trường sản xuất hoàn toàn tốt trong môi trường thử nghiệm. Thông thường chúng liên quan đến các vấn đề về bộ nhớ hoặc hiệu quả CPU. Nhưng chúng không bao giờ liên quan đến vấn đề sạch sẽ.
{% endhint %}

## 1.4. One Assert per Test

```java
class SerializedPageResponderTest {
	public void testGetPageHierarchyAsXml() throws Exception {
		givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
		whenRequestIsIssued("root", "type:pages");
		thenResponseShouldBeXML();
	}

	public void testGetPageHierarchyHasRightTags() throws Exception {
		givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
		whenRequestIsIssued("root", "type:pages");
		thenResponseShouldContain("<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
	}
}
```

{% hint style='info' %}
Cuốn sách đề cập đến việc có một `assert` cho mỗi bài test, nhưng điều này có thể gây ra mã trùng lặp. Bạn có thể sử dụng `TEMPLATE METHOD pattern` về cơ bản có thiết lập trong lớp cơ sở và sau đó thực hiện `assert`/kiểm tra trong các bài test khác. Sau đó, tác giả tiếp tục nói rằng không nên ngại đưa ra nhiều hơn một `assert` trong một bài test. Điều tốt nhất bạn có thể nói là số lượng `assert` trong một bài test phải được giảm thiểu cũng như chỉ kiểm tra một nội dung duy nhất cho mỗi chức năng kiểm tra. Bạn không muốn các chức năng kiểm tra dài đi kiểm tra một thứ linh tinh khác
{% endhint %}

## 1.5. F.I.R.S.T

- `Fast`: kiểm tra nên nhanh. Vì khi chạy thử chậm, bạn sẽ không muốn chạy chúng thường xuyên.
- `Independent`: Các xét nghiệm không nên phụ thuộc vào nhau. Các xét nghiệm sẽ có thể chạy theo thứ tự bất kỳ.
- `Repeatable`: Các xét nghiệm nên được lặp lại trong bất kỳ môi trường nào. Nếu thử nghiệm của bạn là không thể lặp lại trong bất kỳ môi trường, sau đó bạn sẽ luôn có một cái cớ cho lý do tại sao họ thất bại.
- `Self-Validating`: Các thử nghiệm phải có đầu ra boolean. Bạn không cần phải đọc qua tệp nhật ký để biết các bài kiểm tra có vượt qua hay không.
- `Timely`: Các bài kiểm tra cần phải được viết một cách kịp thời. Kiểm tra đơn vị nên được viết ngay trước mã sản xuất. Nếu bạn viết bài kiểm tra sau khi mã sản xuất, sau đó bạn có thể tìm mã sản xuất để được khó để kiểm tra.

## 1.6. Conclusion

- Các bài kiểm tra cũng quan trọng đối với sức khỏe của một dự án như mã sản xuất. Chúng thậm chí còn quan trọng hơn, bởi vì các bài kiểm tra bảo tồn và tăng cường tính linh hoạt, khả năng bảo trì và khả năng sử dụng lại của mã sản xuất. Vì vậy, hãy luôn giữ cho bài kiểm tra của bạn liên tục sạch sẽ.