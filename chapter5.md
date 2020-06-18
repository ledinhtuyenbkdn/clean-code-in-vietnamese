
# Chapter 5 : Formatting

**Table of Contents:**

- [Chapter 5 : Formatting](#chapter-5--formatting)
  - [1. The Purpose of Formatting](#1-the-purpose-of-formatting)
  - [2. Vertical Formatting](#2-vertical-formatting)
    - [2.1. The Newspaper Metaphor](#21-the-newspaper-metaphor)
    - [2.2. Vertical Openness Between Concepts](#22-vertical-openness-between-concepts)
    - [2.3. Vertical Density (Mật độ)](#23-vertical-density-mật-độ)
    - [2.4. Vertical Distance](#24-vertical-distance)
    - [2.5. Vertical Ordering](#25-vertical-ordering)
  - [3. Horizontal Formatting](#3-horizontal-formatting)
    - [3.1. Horizontal Openness and Density](#31-horizontal-openness-and-density)
    - [3.2. Horizontal Alignment (Dóng hàng)](#32-horizontal-alignment-dóng-hàng)
    - [3.3. Indentation](#33-indentation)
    - [3.4. Dummy Scopes](#34-dummy-scopes)
  - [4. Team Rules](#4-team-rules)

Bạn nên quan tâm đến Code của bạn được định dạng một cách đẹp mắt.

## 1. The Purpose of Formatting

- Điều đầu tiên, hãy `dọn dẹp`. Hình thức của code rất quan trọng.
- <mark>Code Formatting</mark> là một hình thức giao tiếp, và giao tiếp là đơn đặt hàng đầu tiên mà các doanh nghiệp xác định lập trình viên chuyên nghiệp.

**Formatting guidelines** bao gồm:

## 2. Vertical Formatting

- Hầu hết các Java source file có độ lớn như thế nào? Trong Java, file size có liên quan chặt chẽ với class size. Hình dưới đây sẽ cho thấy sự phân bố kích thước các file trong các project khác nhau.

![File Lenght](.\assets\images\chapter5-01.PNG)

- Dường như có thể xây dựng các hệ thống quan trọng trong các tệp thông thường là 200 dòng và cao nhất là 500 dòng.
- Dù đây không là một `hard and fast rule` nhưng nó nên được xem xét. Một file nhỏ thường dễ hiểu hơn file lớn

### 2.1. The Newspaper Metaphor

>Hãy nghĩ như viết một bài báo tốt. Bạn đọc nó từ trên xuống. Ở phía trên, tiêu đề cho bạn biết bài viết đang nói về cái gì. Đoạn đầu tiên tóm tắt toàn bộ câu chuyện, ẩn các chi tiết và mang những khái niệm trải rộng ra. Càng đi xuống các chi tiết sẽ được tăng lên cho đến khi bạn nắm được đầy đủ các ý.

-> Chúng ta muốn source file giống như một `bài báo`. Tên đơn giản nhưng giải thích rõ ràng. Cái tên cho chúng ta biết có đang ở đúng module hay không. Ở phía đầu cung cấp những khái niệm ở cấp cao và các thuật toán. Chi tiết sẽ tăng thêm khi chúng ta di chuyển xuống dưới. Cho đến khi kết thúc, chúng ta tìm những hàm ở mức thấp nhất và chi tiết của source file.

### 2.2. Vertical Openness Between Concepts

Gần như tất cả Code đề được đọc từ trái sang phải, từ trên xuống dưới. Mỗi dòng đều đại diện cho một biểu thức hoặc một mệnh đề. Những khái niệm nên được tách ra với một `dòng trắng`. => Tăng khả năng đọc Code

Ví dụ:

<span style="color:red">**Bad**</span>

```java
package fitnesse.wikitext.widgets;
import java.util.regex.*;
public class BoldWidget extends ParentWidget {
    public static final String REGEXP = "'''.+?'''";
    private static final Pattern pattern = Pattern.compile("'''(.+?)'''",
        Pattern.MULTILINE + Pattern.DOTALL);
    public BoldWidget(ParentWidget parent, String text) throws Exception {
        super(parent);
        Matcher match = pattern.matcher(text);
        match.find();
        addChildWidgets(match.group(1));}
    public String render() throws Exception {
        StringBuffer html = new StringBuffer("<b>");
        html.append(childHtml()).append("</b>");
        return html.toString();
    }
}
```

<span style="color:green">**Good**</span>

```java
package fitnesse.wikitext.widgets;

import java.util.regex.*;

public class BoldWidget extends ParentWidget {
    public static final String REGEXP = "'''.+?'''";
    private static final Pattern pattern = Pattern.compile("'''(.+?)'''",
        Pattern.MULTILINE + Pattern.DOTALL
    );

    public BoldWidget(ParentWidget parent, String text) throws Exception {
        super(parent);
        Matcher match = pattern.matcher(text);
        match.find();
        addChildWidgets(match.group(1));
    }

    public String render() throws Exception {
        StringBuffer html = new StringBuffer("<b>");
        html.append(childHtml()).append("</b>");
        return html.toString();
    }
}
```

### 2.3. Vertical Density (Mật độ)

Sự rộng rãi chia tách các khái niệm, còn mật độ liên kết chặt chẽ lại.

<span style="color:red">**Bad**</span>

```java
public class ReporterConfig {
    /**
    * The class name of the reporter listener
    */
    private String m_className;

    /**
    * The properties of the reporter listener
    */
    private List<Property> m_properties = new ArrayList<Property>();

    public void addProperty(Property property) {
        m_properties.add(property);
    }
}
```

=> Comment làm phá hỏng sự liên kết giữa 2 biến:

<span style="color:green">**Good**</span>

```java
public class ReporterConfig {
    private String m_className;
    private List<Property> m_properties = new ArrayList<Property>();

    public void addProperty(Property property) {
        m_properties.add(property);
    }
}
```

### 2.4. Vertical Distance

Các khái niệm có `liên quan` với nhau nên được giữ `gần nhau`, tránh tách chúng ra thành từng file khác nhau trừ khi có lý do chính đáng.

<mark>**Khai báo biến:**</mark>

- Nên khai báo biến ở càng gần nơi mà nó sử dụng càng tốt.
- Bởi vì hàm ngắn, nên biến cục bộ nên đặt **đầu** mỗi hàm.

```java
private static void readPreferences() {
    InputStream is= null;
    try {
        is= new FileInputStream(getPreferencesFile());
        setPreferences(new Properties(getPreferences()));
        getPreferences().load(is);
    } catch (IOException e) {
        try {
            if (is != null)
                is.close();
        } catch (IOException e1) {
        }
    }
}
```

- Các biến điều khiển cho vòng lặp nên được khai báo ở trong vòng lặp.

```java
public int countTestCases() {
    int count= 0;
    for (Test each : tests)
        count += each.countTestCases();
    return count;
}
```

<mark>**Biến đối tượng:**</mark>

- Nên được khai báo trên `đầu class`.
- Không nên tăng khoảng cách dọc của các biến này vì trong 1 class được thiết kế tốt thì những biến này được sử dụng bởi nhiều (if not all) phương thức của class.

Ví dụ: `TestSuite` class

```java
public class TestSuite implements Test {
    static public Test createTest(Class<? extends TestCase> theClass,
    String name) {
        ...
    }

    public static Constructor<? extends TestCase>
    getTestConstructor(Class<? extends TestCase> theClass)
    throws NoSuchMethodException {
        ...
    }

    public static Test warning(final String message) {
        ...
    }

    private static String exceptionToString(Throwable t) {
        ...
    }

    private String fName;

    private Vector<Test> fTests= new Vector<Test>(10);

    public TestSuite() {
    }

    public TestSuite(final Class<? extends TestCase> theClass) {
        ...
    }

    public TestSuite(Class<? extends TestCase> theClass, String name) {
        ...
    }
    ... ... ... ... ...
}
```

<mark>**Hàm phụ thuộc:**</mark>

- Nếu một hàm gọi một hàm khác thì nên để chúng gần nhau. `Hàm gọi nên ở trước hàm bị gọi` -> dòng chảy tự nhiên trong chương trình.

Ví dụ:

```java
public class WikiPageResponder implements SecureResponder {
    protected WikiPage page;
    protected PageData pageData;
    protected String pageTitle;
    protected Request request;
    protected PageCrawler crawler;

    public Response makeResponse(FitNesseContext context, Request request)
    throws Exception {
        String pageName = getPageNameOrDefault(request, "FrontPage");
        loadPage(pageName, context);
        if (page == null)
            return notFoundResponse(context, request);
        else
            return makePageResponse(context);
    }

    private String getPageNameOrDefault(Request request, String defaultPageName)
    {
        String pageName = request.getResource();
        if (StringUtil.isBlank(pageName))
            pageName = defaultPageName;
        return pageName;
    }

    protected void loadPage(String resource, FitNesseContext context)
    throws Exception {
        WikiPagePath path = PathParser.parse(resource);
        crawler = context.root.getPageCrawler();
        crawler.setDeadEndStrategy(new VirtualEnabledPageCrawler());
        page = crawler.getPage(context.root, path);
        if (page != null)
            pageData = page.getData();
    }

    private Response notFoundResponse(FitNesseContext context, Request request)
    throws Exception {
    return new NotFoundResponder().makeResponse(context, request);
    }

    private SimpleResponse makePageResponse(FitNesseContext context)
    throws Exception {
        pageTitle = PathParser.render(crawler.getFullPath(page));
        String html = makeHtml(context);
        SimpleResponse response = new SimpleResponse();
        response.setMaxAge(0);
        response.setContent(html);
        return response;
    }
}
```

<mark>**Conceptual Affinity:**</mark> (sự tương đồng về khái niệm)

- Certain bits of code `want` to be near other bits -> Chúng có một mối quan hệ khái niệm nhất định. Mối quan hệ này càng mạnh thì nên có càng ít vertical distance giữa chúng.
- Mối quan hệ đó có thể được gây ra do một nhóm chức năng thực hiện một hoạt động tương tự.

Ví dụ:

```java
public class Assert {
    static public void assertTrue(String message, boolean condition) {
        if (!condition)
            fail(message);
    }

    static public void assertTrue(boolean condition) {
        assertTrue(null, condition);
    }

    static public void assertFalse(String message, boolean condition) {
        assertTrue(message, !condition);
    }

    static public void assertFalse(boolean condition) {
        assertFalse(null, condition);
    }
...
```

### 2.5. Vertical Ordering

- Hàm chức năng được gọi thì nên ở phía dưới hàm gọi nó. Tạo một dòng chảy mã nguồn từ mức cao đến mức thấp.

## 3. Horizontal Formatting

Cố gắng giữ cho dòng được ngắn. Đừng bao giờ phải di chuyển sang phải.

### 3.1. Horizontal Openness and Density

Sử dụng khoảng trắng để kết hợp những thứ có liên quan và tách những thứ ít liên quan hơn với nhau.

Ví dụ:

```java
private void measureLine(String line) {
  lineCount++;
  int lineSize = line.length();
  totalChars += lineSize();
  lineWidthHistogram.addLine(lineSize, lineCount);
  recordWidestLine(lineSize);
}
```

-> Chúng ta không đặt khoảng cách giữa tên function và dấu ngoặc - vì function và đối số của nó có liên quan chặt chẽ với nhau. Tách các đối số trong hàm gọi dấu ngoặc đơn để làm nổi bật dấu `,` và cho thấy các đối số là riêng lẻ.

Ví dụ 2:

```java
public class Quadratic {
  public static double root1(double a, double b, double c) {
    double determinant = determinant(a, b, c);
    return (-b + Math.sqrt(determinant) / (2*a);
  }
  private static double determinant(double a, double b, double c) {
    return b*b - 4*a*c;
  }
}
```

-> Dùng khoảng trắng để nhấn mạnh độ ưu tiên của toán tử. Yếu tố không có khoảng trắng - ưu tiên cao, có khoảng trắng - ưu tiên thấp

### 3.2. Horizontal Alignment (Dóng hàng)

Ví dụ:

```java
private   Long            requestParsingTimeLimit;
protected Request         request;
private   FitNesseContent context;
this.context =            context;
input =                   s.getInputStream()
requestParsingTimeLimit = 900;
```

Dóng hàng như vậy không thực sự hữu ích. Cách này như làm nổi lên những vấn đề sai trái và làm đi lệch ra khỏi mục đích thật sự. Không cần phải dóng hàng theo từng cột.

### 3.3. Indentation

- Source file là một hệ thống phân cấp thay vì liên kết và phác thảo.
- Mỗi mức của hệ thống cấp bậc này là 1 phạm vi mà tên được khai báo và trong khai báo đó những câu lệnh thực thi được giải thích.
- Để làm hiển thị những phân cấp này, chúng ta thụt dòng mã code tương ứng với vị trí của nó trong hệ thống cấp độ.

<span style="color:red">**Bad**</span>

```java
public class FitNesseServer implements SocketServer { private FitNesseContext
context; public FitNesseServer(FitNesseContext context) { this.context =
context; } public void serve(Socket s) { serve(s, 10000); } public void
serve(Socket s, long requestTimeout) { try { FitNesseExpediter sender = new
FitNesseExpediter(s, context);
sender.setRequestParsingTimeLimit(requestTimeout); sender.start(); }
catch(Exception e) { e.printStackTrace(); } } }
```

<span style="color:green">**Good**</span>

```java
public class FitNesseServer implements SocketServer {
    private FitNesseContext context;
    public FitNesseServer(FitNesseContext context) {
        this.context = context;
    }
    public void serve(Socket s) {
        serve(s, 10000);
    }
    public void serve(Socket s, long requestTimeout) {
        try {
            FitNesseExpediter sender = new FitNesseExpediter(s, context);
            sender.setRequestParsingTimeLimit(requestTimeout);
            sender.start();
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

<mark>**Breaking Indentation:**</mark> Đôi khi với những câu lệnh ngắn, vòng lặp ngắn hoặc function ngắn thì chúng ta hay có những cám dỗ không đặt indentation để cho code ngắn gọn. -> phá vỡ quy tắc thụt dòng.

<span style="color:red">**Bad**</span>

```java
public class CommentWidget extends TextWidget
{
    public static final String REGEXP = "^#[^\r\n]*(?:(?:\r\n)|\n|\r)?";

    public CommentWidget(ParentWidget parent, String text){super(parent, text);}
    public String render() throws Exception {return ""; }
}
```

<span style="color:green">**Good**</span>

```java
public class CommentWidget extends TextWidget {
    public static final String REGEXP = "^#[^\r\n]*(?:(?:\r\n)|\n|\r)?";

    public CommentWidget(ParentWidget parent, String text){
        super(parent, text);
    }

    public String render() throws Exception {
        return "";
    }
}
```

### 3.4. Dummy Scopes

Đôi khi thân 1 câu lệnh `while` or `for` là 1 `dummy`.

```java
while (dis.read(buf, 0, readBufferSize) != -1)
   ;
```

## 4. Team Rules

Mỗi lập trình viên đều có một nguyên tắc định dạng riêng. nhưng khi làm việc nhóm phải tuân theo nguyên tắc của nhóm. Một nhóm phát triển nên thỏa thuận về một kiểu định dạng duy nhất, sau đó mỗi thành viên trong nhóm nên sử dụng kiểu định dạng đó. => Tính nhất quán.
