
# Chapter 12: Emergence

**Table of Contents**

- [Chapter 12: Emergence](#chapter-12-emergence)
  - [Getting Clean vi Emergent Design](#getting-clean-vi-emergent-design)
  - [Simple Design Rule 1: Run All the Tests](#simple-design-rule-1-run-all-the-tests)
  - [Simple Design Rule 2: Refactoring](#simple-design-rule-2-refactoring)
  - [No duplication](#no-duplication)
  - [Expressive](#expressive)
  - [Minimal Classes and Methods](#minimal-classes-and-methods)
  - [Conclusion](#conclusion)

## Getting Clean vi Emergent Design

**4 nguyên tắc thiết kế `Simple Design` của Ken Beck's sẽ giúp chúng ta tạo ra các phần mềm có thiết kế tốt:**

1. Run all Tests

2. Contains no duplication

3. Expresses the intent of the programmer

4. Minimizes the number of classes and methods

_Các quy tắc này có tính thứ tự._

## Simple Design Rule 1: Run All the Tests

- Một hệ thống được kiểm tra và vượt qua tất cả các tests được gọi là một `test-able system` -> Đủ điều kiện deploy

- Làm cho hệ thống có khả năng `testable` đưa chúng ta đến thiết kế mà tất cả các class trở nên `small` và `single purpose` -> đáp ứng `SRP`. => <mark>Một hệ thống với fully testable sẽ giúp chúng ta tạo nên những thiết kế tốt</mark>

- `Tight coupling` làm cho chúng ta khó có thể viết test -> Áp dụng DIP, dependency injection, interfaces, abstraction giúp chúng ta `minimize coupling`

{% hint style='info' %}
- Mục tiêu chính của OO là `low coupling` và `high cohesion`. Viết test tốt để dẫn đến thiết kế tốt hơn.

{% endhint %}

## Simple Design Rule 2: Refactoring

- Khi đã có các tests, chúng ta tiếp tục thực hiện việc làm cho `code` và `classes` clean. => <mark>incrementally refactoring the code</mark>

- Tests giúp chúng ta đảm bảo rằng các thay đổi của quá trình refactoring không làm phá vỡ bất cứ điều gì

- Khi refactoring, chúng ta có thể áp dụng các kỹ thuật để thiết kế phần mềm tốt. Chúng tôi có thể làm tăng sự gắn kết (increase cohesion), giảm khớp nối (decrease coupling), tách biệt các concern, modularize system concerns, tách các funtion và class, chọn tên tốt hơn,... Tiếp theo, ta sẽ áp dụng 3 luật còn lại để có được `simple design`:

  - Eliminate duplication
  
  - Ensure expressiveness
  
  - Minimize the number of classes and methods

## No duplication

- <mark>Duplication là kẻ thù chính của một hệ thống thiết kế tốt (well-designed system)</mark> => Additional work, additional risk và additional unnecessary complexity

- Duplication được thể hiện ở sự trùng lặp về dòng code, cũng có thể là sự trùng lặp về implement

Xem xét ví dụ:

```java
int size() {}
boolean isEmpty() {}
```
Ta có thể giảm thiểu duplication bằng cách:

```java
boolean isEmpty() {
  return 0 == size();
}
```

- Một `clean system` yêu cầu loại bỏ duplication, <mark>cho dù chỉ là vài dòng code</mark>

Xem xét ví dụ:

```java
public void scaleToOneDimension(float desiredDimension, float imageDimension) {

  if (Math.abs(desiredDimension - imageDimension) < errorThreshold) {
    return;
  }

  float scalingFactor = desiredDimension / imageDimension;
  scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);

  RenderedOp newImage = ImageUtilities.getScaledImage(image, scalingFactor, scalingFactor);

  image.dispose();
  System.gc();
  image = newImage;

}

public synchronized void rotate(int degrees) {
  RenderedOp newImage = ImageUtilities.getRotatedImage(image, degrees);
  image.dispose();
  System.gc();
  image = newImage;
}
```

Ta thực hiện loại bỏ sự trùng lặp ở `scaleToOneDimension` and `rotate` methods:

```java
public void scaleToOneDimension(float desiredDimension, float imageDimension) {

  if (Math.abs(desiredDimension - imageDimension) < errorThreshold) {
    return;
  }

  float scalingFactor = desiredDimension / imageDimension;
  scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);

  replaceImage(ImageUtilities.getScaledImage(image, scalingFactor, scalingFactor));

}

public synchronized void rotate(int degrees) {
  replaceImage(ImageUtilities.getRotatedImage(image, degrees));
}

private void replaceImage(RenderedOp newImage) {
  image.dispose();
  System.gc();
  image = newImage;
}
```
- <mark>Understanding how to achieve reuse in the small is essential to achieving reuse in the large.</mark>


The <mark>TEMPLATE METHOD pattern</mark> (GOF) is a common technique for removing higher-level duplication:

```java
public class VacationPolicy {
  public void accrueUSDivisionVacation() {
    // code to calculate vacation based on hours worked to date
    // ...
    // code to ensure vacation meets US minimums
    // ...
    // code to apply vaction to payroll record
    // ...
  }
  public void accrueEUDivisionVacation() {
    // code to calculate vacation based on hours worked to date
    // ...
    // code to ensure vacation meets EU minimums
    // ...
    // code to apply vaction to payroll record
    // ...
  }
}
```
Sử dụng TEMPLATE METHOD:
```java
abstract public class VacationPolicy {
  public void accrueVacation() {
    calculateBaseVacationHours();
    alterForLegalMinimums();
    applyToPayroll();
  }
  private void calculateBaseVacationHours() { /* ... */ };
  abstract protected void alterForLegalMinimums();
  private void applyToPayroll() { /* ... */ };
}
```

```java
public class USVacationPolicy extends VacationPolicy {
  @Override 
  protected void alterForLegalMinimums() {
    // US specific logic
  }
}
```

```java
public class EUVacationPolicy extends VacationPolicy {
  @Override 
  protected void alterForLegalMinimums() {
    // EU specific logic
  }
}
```

## Expressive

- Chúng ta tạo ra các đoạn code phức tạp -> Chúng ta có hiểu biết sâu về vấn đề. Nhưng đối với việc maintain, sự hiểu biết đó là thấp

- <mark>Phần lớn các chi phí của một dự án phần mềm là trong việc bảo trì dài hạn (long-term maintenance)</mark>

- Hệ thống càng phức tạp, thời gian để hiểu càng lâu, có khả năng kéo theo các hiểu lầm => Tiềm ẩn lỗi trong quá trình maintain, tăng chi phí sản xuất => <mark>Code nên thể hiện rõ ràng, clear ý tưởng của tác giả</mark>

- Chúng ta cần `express yourself`:

  - Chọn tên tốt: variables, functions, classes name,...
  
  - Functions and classes phải `small` -> Dễ chọn tên, dễ viết và dễ hiểu

  - Sử dụng thuật ngữ chuẩn (standard nomenclature)

  - <mark>Sử dụng design pattern</mark> => Chính design pattern là những `standard`, thông qua đó các lập trình viên có thể hiểu lẫn nhau dựa trên một pattern cụ thể

  - Viết unit-tests tốt

{% hint style='info' %}
- Spend a little time with each of your functions and classes. Choose better names, split large functions into smaller functions, and generally just take care of what you’ve created. Care is a precious resource.
{% endhint %}

## Minimal Classes and Methods

- `Elimination of duplication`, `code expressiveness`, và `SPR` là các khái niệm nền móng và được thực hiện ở mức tổng quát hóa

- Nỗ lực làm cho class và method trở nên nhỏ có thể dẫn đến việc tạo quá nhiều `tiny classes` và methods. <mark>Vì vậy, chúng ta luôn chú ý giữ cho số lượng function và class ở mức thấp</mark>

- <mark>Our goal is to keep our overall system small while we are also keeping our functions and classes small</mark>

- Rule này có độ ưu tiên thấp nhất trong 4 rules của `Simple Design`. Nhưng nó lại là quan trọng, để giữ các class và function ở số lượng thấp -> Đó là điều quan trọng cho tests, loại bỏ trùng lặp, và express yourself (thể hiện ý định của người viết code).

## Conclusion

- Không có một tập hợp kinh nghiệm thực thành nào đơn giản có thể thay thể cho <mark>kinh nghiệm</mark>

- `Good principles` và `patterns` được đúc kết trong nhiều thập kỷ kinh nghiệm -> Giúp lập trình viên rút ngắn thời gian tiếp cận thay vì phải mất nhiều năm để học và trải nghiệm