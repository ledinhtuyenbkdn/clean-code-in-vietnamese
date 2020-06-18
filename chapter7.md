# Chapter 7: Error Handling

- [Chapter 7: Error Handling](#chapter-7-error-handling)
  - [1.1. Use Exceptions Rather Than Return Codes (Sử dụng ngoại lệ thay vì trả lại mã)](#11-use-exceptions-rather-than-return-codes)
  - [1.2. Write Your Try-Catch-Finally Statement First (Viết tuyên bố Try-Catch-Finally đầu tiên của bạn)](#12-write-your-try-catch-finally-statement-first)
  - [1.3. Use Unchecked Exceptions (Sử dụng ngoại lệ không được kiểm tra)](#13-use-unchecked-exceptions)
  - [1.4. Provide Context with Exceptions (Cung cấp ngữ cảnh với ngoại lệ)](#14-provide-context-with-exceptions)
  - [1.5. Define Exception Classes in Terms of a Caller’s Needs (Xác định các lớp ngoại lệ trong điều khoản nhu cầu của người gọi)](#15-define-exception-classes-in-terms-of-a-caller’s-needs)
  - [1.6. Define the Normal Flow (Định nghĩa một luồng bình thường "khi ngoại lệ lẫn lộn với code logic")](#16-define-the-normal-flow)
  - [1.7. Don’t Return Null (Không trả về null)](#17-don’t-return-null)
  - [1.8. Don't Pass Null (Không truyền vào null)](#18-don't-pass-null)
  - [1.9. Conclusion (Kết luận)](#19-conclusion)

- Như chúng ta đã biết thì xử lý lỗi rất quan trọng, nhưng nếu nó che xuất tính logic của bài toán thì dẫn đến nó sẽ sai.

## 1.1. Use Exceptions Rather Than Return Codes(*Sử dụng ngoại lệ thay vì trả lại mã*)

- Khi mà chưa có định nghĩa về ngoại lệ, các kỹ thuật xử lý và báo cáo lỗi bị hạn chế.Ta có thể đặt cờ lỗi hoặc trả lại mã lỗi để người gọi có thể kiểm tra. 
- Ví dụ về không sử dụng exception

```java
public class DeviceController {
	public void sendShutDown() {
		DeviceHandle handle = getHandle(DEV1);
		// Check the state of the device
		if (handle != DeviceHandle.INVALID) {
			// Save the device status to the record field
			retrieveDeviceRecord(handle);
			// If not suspended, shut down
			if (record.getStatus() != DEVICE_SUSPENDED) {
				pauseDevice(handle);
				clearDeviceWorkQueue(handle);
				closeDevice(handle);
			} else {
				logger.log("Device suspended. Unable to shut down");
			}
		} else {
			logger.log("Invalid handle for: " + DEV1.toString());
		}
	}
}
```

- Vấn đề với các phương pháp này là họ làm lộn xộn cho người gọi. Và nó rất dễ để quên.
Vì lý do này, tốt hơn là ném một ngoại lệ khi bạn gặp lỗi. Mã cuộc gọi sạch hơn. Logic của nó không bị che khuất bởi việc xử lý lỗi.

- Ở hàm `getHandle` throw exception `DeviceShutDownError` nếu `Device` không hợp lệ.
- Như vậy thì ở hàm `tryToShutDown()` gọi hàm `getHandle` không cần làm thêm bước kiểm tra `Device` có hợp lệ hay không.

```java
public class DeviceController {

	public void sendShutDown() {
		try {
			tryToShutDown();
		} catch (DeviceShutDownError e) {
			logger.log(e);
		}
	}

	private void tryToShutDown() throws DeviceShutDownError {
		DeviceHandle handle = getHandle(DEV1);
		DeviceRecord record = retrieveDeviceRecord(handle);
		pauseDevice(handle);
		clearDeviceWorkQueue(handle);
		closeDevice(handle);
	}

	private DeviceHandle getHandle(DeviceID id) {
		...
		throw new DeviceShutDownError("Invalid handle for: " + id.toString());
		...
	}
	...
}
```

- Lúc này thuật toán `device shutdown` và xử lý lỗi, hiện đã được tách ra. Bạn có thể xem xét từng mối quan tâm đó và hiểu chúng một cách độc lập.

## 1.2. Write Your Try-Catch-Finally Statement First(*Viết tuyên bố Try-Catch-Finally đầu tiên của bạn*)

- Một trong những điều thú vị nhất về các ngoại lệ là chúng `xác định một phạm vi` trong chương trình.
- Khi bạn mã trong phần try của khối lệnh try-catch-finally được thực thi, Việc thực thi có thể hủy bỏ tại bất kỳ điểm nào (Khi gặp exception)  và sau đó tiếp tục tại catch.
- Trong cách này, khối try giống như một transactions (phiên giao dịch). Catch của bạn sẽ không được thực thi nếu không có vấn đề gì với try. 
- Chúng ta sẽ bắt đầu với Unit test cho trường hợp ngoại lệ do truy cập file không tồn tại.

```java
@Test(expected = StorageException.class)
public void retrieveSectionShouldThrowOnInvalidFileName(){
	sectionStore.retrieveSection("invalid - file");
}
public List<RecordedGrip> retrieveSection(String sectionName) {
	// dummy return until we have a real implementation
	return new ArrayList<RecordedGrip>();
}
```

- Test này sẽ fail do xử lý chưa ném ra ngoại lệ. Tiếp theo ta sẽ thay đổi xử lý truy cập đến một file không tồn tại:

```java
public List<RecordedGrip> retrieveSection(String sectionName) {
   try {
      FileInputStream stream = new FileInputStream(sectionName)
   } catch (Exception e) {
      throw new StorageException("retrieval error", e);
   } finally {
      stream.close(); 
   }
   return new ArrayList<RecordedGrip>();
}
```

- Tại thời điểm này, chúng ta có thể tái cấu trúc. Chúng ta có thể thu hẹp loại ngoại lệ mà chúng ta bắt được để khớp với loại thực tế
được ném FileInputStream: FileNotFoundException

```java
public List<RecordedGrip> retrieveSection(String sectionName) {
   try {
      FileInputStream stream = new FileInputStream(sectionName)
   } catch (FileNotFoundException e) {
      throw new StorageException("retrieval error", e);
   } finally {
      stream.close(); 
   }
   return new ArrayList<RecordedGrip>();
}
```

- Bây giờ chúng ta đã xác định phạm vi với cấu trúc try-catch, Có thể sử dụng TDD để xây dựng phần còn lại của logic mà chúng ta cần.
- Hãy thử viết các trường hợp test buộc phải có ngoại lệ, và sau đó thêm hành vi để xử lý của bạn để đáp ứng các bài kiểm test. Điều này sẽ làm bạn xây dựng được phạm vi transaction của `try block first` và sẽ giúp bạn duy trì bản chất transaction của phạm vi đó.

## 1.3. Use Unchecked Exceptions(*Sử dụng ngoại lệ không được kiểm tra*)

- Phá vỡ 'Nguyên tắc mở/đóng' và 'Đóng gói' là hai lý do chính khiến việc sử dụng `Checked exceptions` trở thành một ý tưởng tồi!
- Trong ví dụ dưới đây, phương thức `MainBookReader.main` đang gọi phương thức `MainBookReader.readJsonObject` để lấy mô tả của sách từ tệp JSON. Và vì `readJsonObject` ném ra một ngoại lệ, nên ta đã phải thêm  mệnh đề `FileNotFoundException` vào chữ ký của phương thức `main` và `interface JsonLoader`.

**<p style="color: #f44336">Bad</p>**

```java
public class MainBookReader {
    public static void main(String[] args) throws FileNotFoundException {
        BookJsonLoader jsonLoader = new BookJsonLoader();
        Book bookFromJson = jsonLoader.readJsonObject("books.json");
        System.out.println(bookFromJson.name());
    }
}
```

```java
public class BookJsonLoader implements JsonLoader {
    public Book readJsonObject(String fileName) throws FileNotFoundException {
        FileReader jsonFile = new FileReader(fileName);
        Gson gson = new Gson();
        return transformToBook(gson.fromJson(jsonFile, JsonBook.class));
    }

    private Book transformToBook(JsonBook jsonBook) {
        return new Book(jsonBook.getName());
    }
}
```

```java
public interface JsonLoader {
    Book readJsonObject(String fileName) throws FileNotFoundException;
}
```

- Các phương thức & lớp hàng đầu của chúng ta đã phải biết rất nhiều về các phương thức & lớp thấp hơn! (lower methods & classes!)
- Nếu bất kỳ thay đổi nào được áp dụng cho các phương thức thấp hơn, thì đồng nghĩa với việc ta phải thay đổi cho tất cả các phương thức và lớp ở cấp cao hơn.
- => Đây là vi phạm 'Nguyên tắc mở/đóng' và 'Đóng gói'!

**<p style="color: #4caf50">Good</p>**

- Trong mệnh đề `catch`, tôi đang ném một  `JsonFileNotFoundException (UncheckedException)` mà chúng ta tự tạo ra.

```java
public class BookJsonLoader implements JsonLoader {
    public Book readJsonObject(String fileName){
        try {
            FileReader jsonFile = new FileReader(fileName);
            Gson gson = new Gson();
            return transformToBook(gson.fromJson(jsonFile, JsonBook.class));
        } catch (FileNotFoundException e) {
            throw new JsonFileNotFoundException("Can't find the file " + fileName + ". Please make sure it exists!", e);
        }
    }

    private Book transformToBook(JsonBook jsonBook) {
        return new Book(jsonBook.getName());
    }
}
```

```java
public class JsonFileNotFoundException extends RuntimeException{
	
	JsonFileNotFoundException(String message){
		super(message);
	}
	
} 

```

- Lợi ích của cách làm trên là:

+ Loại bỏ được tất cả các `throws clauses`.

   + Ẩn logic và thông tin của lớp chúng ta khỏi các lớp bên ngoài.

   + Gửi tin nhắn thông tin cho người dùng trong trường hợp không tìm thấy tệp.

   + Giảm thiểu sự phụ thuộc (loại bỏ imports từ các lớp khác).

## 1.4. Provide Context with Exceptions(*Cung cấp ngữ cảnh với ngoại lệ*)

- Mỗi exception mà bạn "ném ra" phải cung cấp đủ ngữ cảnh để xác định nguồn gốc và vị trí của nó.
- Ở java thì ta có thể lấy dấu vết lỗi(stack trace) từ bất kì ngoại lệ nào. Tuy nhiên, một stack dấu vết không thể cho bạn biết mục đích của hoạt động mà thất bại.
- Tạo tin nhắn thông báo lỗi và vượt qua chúng cùng với ngoại lệ của bạn. Đề cập đến các hoạt động không thành công và loại thất bại.

## 1.5. Define Exception Classes in Terms of a Caller’s Needs(*Xác định các lớp ngoại lệ trong điều khoản nhu cầu của người gọi*)

- Phần này để cập đến việc xử lý lỗi đến từ bên thứ 3 (Có thể là API hoặc các dịch vụ bên thứ 3).
- Có rất nhiều cách để phân loại lỗi. Ta có thể phân loại lỗi theo nguồn gốc của chúng: chúng đến từ thành phần này, hay là thành phần khác, hoặc là loại của nó. Nó có thể lỗi thiết bị, lỗi mạng, hay là lỗi lập trình...
- Tuy nhiên, Nên định nghĩa các lớp ngoại lệ trong một ứng dụng, mối quan tâm quan trọng nhất của chúng ta nên là chúng bị bắt như thế nào.
- Xem ví dụ bên dưới,

**<p style="color: #f44336">Bad</p>**

```java
ACMEPort port = new ACMEPort(12);
try {
   port.open();
} catch (DeviceResponseException e) {
   reportPortError(e);
   logger.log("Device response exception", e);
} catch (ATM1212UnlockedException e) {
   reportPortError(e);
   logger.log("Unlock exception", e);
} catch (GMXError e) {
   reportPortError(e);
   logger.log("Device response exception");
} finally {
   …
}
```

- Thay vì ta dùng trực tiếp lớp `ACMEPort` thì ta sẽ đóng gói nó vào lớp `LocalPort`. Và sẽ dùng gián tiếp qua lớp `LocalPort` này.
- Lớp `LocalPort` của chúng ta là một trình bao bọc đơn giản để nắm bắt và dịch các ngoại lệ được ném bởi lớp `ACMEPort`:

**<p style="color: #4caf50">Good</p>**

```java
LocalPort port = new LocalPort(12);
try {
   port.open();
} catch (PortDeviceFailure e) {
   reportError(e);
   logger.log(e.getMessage(), e);
} finally {
   …
}

public class LocalPort {
	 private ACMEPort innerPort;
	 
	 public LocalPort(int portNumber) {
		 innerPort = new ACMEPort(portNumber);
	 }
	 
	 public void open() {
		 try {
			 innerPort.open();
		 } catch (DeviceResponseException e) {
			 throw new PortDeviceFailure(e);
		 } catch (ATM1212UnlockedException e) {
			 throw new PortDeviceFailure(e);
		 } catch (GMXError e) {
			 throw new PortDeviceFailure(e);
		 }
	 	}
	 …
	}
}
```

- `Wrappers` như cái mà chúng ta đã định nghĩa `ACMEPort` có thể rất hữu ích. Trong thực tế, đóng gói API của bên thứ ba là một thực tiễn tốt nhất.
- Khi bạn gói API của bên thứ 3 thì bạn có thể giảm thiểu sự phụ thuộc của mình vào nó: bạn có thể chọn để di chuyển đến một thư viện khác trong tương lai.
- Đóng gói API của bên thứ 3 thì giúp ta dễ thự hiện test hơn.
- Một lợi thế cuối cùng của việc đóng gói là bạn không bị ràng buộc với các lựa chọn thiết kế API của một nhà cung cấp. 

## 1.6. Define the Normal Flow(*Định nghĩa một luồng bình thường "khi ngoại lệ lẫn lộn với code logic"*)

- Không bao giờ được sử dụng các Exception trong các luồng điều khiển, khối lệnh điều kiện. Vì nó không hiệu quả và khó đọc.
- Bao bọc, đóng gói các API bên ngoài để có thể đưa ra các ngoại lệ của riêng mình và xác định một trình xử lý bên trên mã của mình để có thể xử lý mọi tính toán bị hủy bỏ.

**<p style="color: #f44336">Bad</p>**

```java
try {
   MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
   m_total += expenses.getTotal();
} catch(MealExpensesNotFound e) {
   m_total += getMealPerDiem();
}
```

- Trong nghiệp vụ này, nếu nhân viên có ăn bữa ăn thì chí phí bữa ăn sẽ được cộng vào tổng số. Ngược lại thì họ sẽ nhận được `meal per diem amount` cho ngày hôm đó. Ngoại lệ làm mờ logic, ta cải thiện chúng như sau:

**<p style="color: #4caf50">Good</p>**

```java
MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
m_total += expenses.getTotal();
```

- Chúng ta có thể thay đổi ExpenseReportDAO để nó luôn luôn trả về một đối tượng MealExpense. Nếu không có chi phí bữa ăn, nó trả về một đối tượng MealExpense trả về per diem dưới dạng tổng của nó:

```java
public class PerDiemMealExpenses implements MealExpenses {
 public int getTotal() {
 // return the per diem default
 }
}
```

- Phương pháp đó gọi là `SPECIAL CASE PATTERN` : Bạn tạo ra một lớp hoặc config một object để nó xử lý các trường hợp đặc biệt cho bạn.
- Khi bạn làm như vậy, mã máy khách không phải xử lý hành vi đặc biệt. Hành vi đó được gói gọn trong đối tượng trường hợp đặc biệt.

## 1.7. Don’t Return Null(*Không trả về null*)

- Trong một số trường hợp thì ta có xu hướng trả về `null` thay vì ném ra một UnCheckedException. Điều này có thể dẫn đến có thể xảy ra một `NullPointerException` sau này.

```java
public void registerItem(Item item) {
    if (item != null) {
        ItemRegistry registry = peristentStore.getItemRegistry();
        if (registry != null) {
            Item existing = registry.getItem(item.getID());
            if (existing.getBillingPeriod().hasRetailOwner()) {
            existing.register(item);
            }
        }
    }
}
```

- Nếu một hàm của chúng ta có thể trả về null => Khi gọi hàm này thì ta cần phải thực hiện thêm bước kiểm tra null.
- Xem đoạn code trên thì nếu `persistentStore` là `null` thì nó sẽ sinh ra một NullPointerException trong khi chạy.
- Nếu bạn muốn trả về `null` từ một phương thức, hoặc khi gọi một phương thức từ API trả về `null` thì hãy xem xét đến việc ném ra một ngoại lệ hoặc là trả về một `SPECIAL CASE object instead`

- Trong nhiều trường hợp thì `special case objects` là một `easy remedy`, xem ví dụ bên dưới nhé: 

```java
List<Employee> employees = getEmployees();
if (employees != null) {
    for(Employee e : employees) {
        totalPay += e.getPay();
    }
}
```

- Hiện tại `getEmployees()` có thể trả về null. Nếu chúng ta thay đổi `getEmployee()` để rằng nó trả về một danh sách trống, chúng ta có thể làm sạch mã

```java
List<Employee> employees = getEmployees();
for(Employee e : employees) {
   totalPay += e.getPay();
}
```

- Java có Collections.emptyList (), và nó trả về một danh sách bất biến xác định trước rằng chúng ta có thể sử dụng cho mục đích này:

```java
public List<Employee> getEmployees() {
   if( .. there are no employees .. )
      return Collections.emptyList(); 
// Trả về một danh sách xác định trước không thay đổi trong Java
}
```

- Nếu bạn code theo cách này, bạn sẽ giảm thiểu cơ hội cho NullPointerExceptions và code của bạn sẽ rõ ràng hơn.

## 1.8. Don't Pass Null(*Không truyền vào null*)

- Phương thức trả về `null` đã là tồi tệ rồi nhưng truyền `null` vào 1 phương thức còn tệ hại hơn.
- Trừ khi bạn làm việc với API, nó yêu cầu bạn truyền vào `null` còn nếu không thì nên hạn chế truyền `null` khi gọi hàm bất cứ khi nào có thể.
- Xét ví dụ về phương thức tính toán cho 2 điểm như bên dưới.

```java
public class MetricsCalculator
{
    public double xProjection(Point p1, Point p2) {
        return (p2.x – p1.x) * 1.5;
    }
    ...
}
```

- Chuyện gì sẽ xảy ta nếu truyền null vào như 1 đối số của phương thức này.

```java
calculator.xProjection(null, new Point(12, 13));
```

- Ta sẽ nhận được 1 NullPointerException khi chạy.
- Vậy làm sao để fix nó? Chúng ta có thể tạo một loại ngoại lệ mới và ném nó.

```java
public class MetricsCalculator
{
    public double xProjection(Point p1, Point p2) {
        if (p1 == null || p2 == null) {
            throw InvalidArgumentException("Invalid argument for MetricsCalculator.xProjection");
        }
        return (p2.x – p1.x) * 1.5;
    }
}
```

- Nó có thể tốt hơn một chút so với `NullPointerException`. Nhưng nếu làm theo cách trên thì ta định nghĩa và xử lý cho `InvalidArgumentException`. 
- Có một thay có thế tốt hơn

```java
public class MetricsCalculator
{
   public double xProjection(Point p1, Point p2) {
      assert p1 != null : "p1 should not be null";
      assert p2 != null : "p2 should not be null";
      return (p2.x – p1.x) * 1.5;
   }
}
```

## 1.9. Conclusion(*Kết luận*)

- Sử dụng các exception thay vì trả về giá trị để các lớp gọi nó đỡ phải xử lý tiếp.
- Viết các đoạn code thành Try-Catch-Finally để dễ quan sát các xử lý code và ngoại lệ.
- Cung cấp đầy đủ thông tin ngoại lệ nhất (có thể ghi log) để dễ dàng debug.
- Nếu exception có code logic hoặc các exception gây ra bởi bên thứ 3, cân nhắc "wrapping"- đóng gọi lại trong một lớp ngoại lệ mới ta xây dựng.
- Chưa có cách hoàn hảo nhất để xử lý với null, hạn chế truyền và nhận nó vào các hàm.