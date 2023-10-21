# LINQ Method Syntax

## Giới thiệu về phương thức mở rộng

**Phương thức mở rộng** (Extension Method) là các phương thức được thêm vào một kiểu đã có sẵn mà không cần phải kế thừa lại, biên dịch lại hay chỉnh sửa gì lên kiểu dữ liệu gốc. Các phương thức mở rộng được khai báo như là các **phương thức tĩnh** (Static Methods), nhưng lại được gọi giống như các phương thức thông thường (được gọi thông qua đối tượng cụ thể).

Các phương thức mở rộng có sẵn trong C# có thể dễ dàng bắt gặp nhất trong các kiểu dẫn xuất của `IEnumerable` và `IEnumerable<T>`, hay còn được biết đến là các phương thức mở rộng của LINQ.

Các phương thức mở rộng trong C# đều được đánh dấu *(extension)* phía trước phương thức. Trước khi tìm hiểu các phương thức mở rộng LINQ, ta sẽ học cách tạo ra các phương thức mở rộng này cho chính các kiểu dữ liệu của bản thân tạo ra.

## Khai báo và định nghĩa phương thức mở rộng

Việc tạo ra các phương thức mở rộng cho một kiểu dữ liệu có sẵn khá đơn giản, cụ thể như sau:

* Tạo ra một **lớp tĩnh** (Static Class) để chứa các phương thức mở rộng.

* Các phương thức mở rộng là các **phương thức tĩnh** trong lớp vừa tạo ra.

* Điều khác biệt giữa phương thức mở rộng và các phương thức thường đó là tham số đầu tiên chỉ định kiểu dữ liệu cần mở rộng (kiểu sẽ nhận phương thức mở rộng). Tham số này phải bắt đầu bằng từ khóa `this`.

**Ví dụ:**

```cs
    public class Student
    {
        public int Id { get; set; }
        public string Name { get; set; }
        // các thành viên khác …
    }

    // lớp chứa phương thức mở rộng
    public static class Extension
    {
        public static void Print(this Student stu)
        {
            Console.WriteLine(stu.Id + "\t" + stu.Name);
        }
    }

    // gọi phương thức Print() bằng đối tượng Student
    var student = new Student() { Id = 1, Name = "Tom" };
    student.Print(); 
```

Ở ví dụ trên, với tham số `this Student stu`, lớp `Student` sẽ nhận phương thức `Print()` làm phương thức mở rộng. Ta có thể gọi phương thức `Print()` thông qua đối tượng `Student` như các thành viên khác trong lớp này.

> [!Note]
> * Tham số đầu tiên trong phương thức mở rộng bắt đầu với từ khóa `this` chỉ có nhiệm vụ xác định kiểu được mở rộng và trình biên dịch không xem nó là tham số cần truyền. Muốn xác định tham số bổ sung thì phải bắt đầu khai báo từ tham số thứ 2 trở đi.
>
> * Mọi kiểu dữ liệu trong C# đều có thể khai báo các phương thức mở rộng, bao gồm cả các kiểu dữ liệu được tạo sẵn (`int`, `string`, `double`, ...).
>
> * Một lớp tĩnh có thể chứa nhiều phương thức mở rộng. Các phương thức này không nhất thiết chỉ mở rộng cho một kiểu. Ở đây ta có thể hiểu, lớp tĩnh chỉ là vùng chứa các phương thức mở rộng, còn chúng có thể mở rộng cho bất kỳ kiểu dữ liệu nào.

**Ví dụ:**

```cs
    public static class Extension
    {
        // mở rộng cho kiểu 'Student'
        public static void method1(this Student stu) 
        {
            // code …
        }

        // mở rộng cho kiểu string
        public static void method2(this string str) 
        {
            // code …
        }
    }
```

> [!Warning]
> * Các phương thức mở rộng không được truy cập vào các dữ liệu riêng tư (`private` hoặc `protected`) của lớp chính.
>
> * Nếu phương thức mở rộng bị xung đột đối với phương thức đã có trong lớp chính, phương thức mở rộng sẽ bị ẩn đi.

### Tìm hiểu một số phương thức LINQ thông dụng

LINQ có 3 ứng dụng chính: 

* **LINQ to Objects** (thao tác với tập hợp)

*  **LINQ to SQL** (thao tác với cơ sở dữ liệu)

*  **LINQ to XML** (thao tác với XML).

Ở đây ta sẽ tìm hiểu ở mức đơn giản nhất, đó là **LINQ to Objects**, vì nó hỗ trợ cho cả **.NET Core** và **.NET Framework**.

Các phương thức mở rộng của LINQ có tên gần giống với các lệnh trong SQL như `Select()`, `Where()`, `OrderBy()`, … thường được dùng để truy vấn dữ liệu trên tập hợp và trả về kết quả truy vấn được.

Các phương thức này thường được cung cấp cho các kiểu dẫn xuất của `IEnumerable` và `IEnumerable<T>` như mảng, `List<T>`, ... và được định nghĩa trong lớp tĩnh `Enumerable`.

Các tham số trong các phương thức mở rộng của LINQ đa phần sử dụng các Delegate quen thuộc như `Func<>`, `Action<>`, … nhằm cho người dùng tự quyết cách thao tác với dữ liệu.

Đối số truyền vào cho kiểu `delegate` có thể là phương thức ẩn danh, biểu thức Lambda hay hàm cục bộ. Tuy nhiên, phổ biến và đơn giản nhất vẫn là biểu thức Lambda.

> [!Important]
> Để sử dụng LINQ và các phương thức mở rộng, hãy khai báo sử dụng `System.Linq`.

### Phương thức Select()

Phương thức mở rộng `Select()` được dùng để truy vấn dữ liệu trong tập hợp và trả về một tập hợp kiểu `IEnumerable<T>`, với `T` là kiểu đang thao tác.

Phương thức này rất thích hợp để sàng lọc thuộc tính cần trả về, thay đổi cách hiển thị, …

Cú pháp:

```cs
    IEnumerable<TResult> Select(Func<TSource, TResult> selector)
    IEnumerable<TResult> Select(Func<TSource, int, TResult> selector)
```

Trong đó:

* `TSource` chính là kiểu của đối tượng gọi đến phương thức `Select()`.

* `TResult` là kiểu sau khi sàng lọc, tính toán và trả về (xem ví dụ để hiểu thêm).

* Tham số kiểu `int` dùng để đại diện cho chỉ số phần tử. Có thể dùng khi cần tương tác giữa giá trị và chỉ số.

Đối với kiểu trả về là `IEnumerable<TResult>`, ta có thể dùng từ khóa `var` để khai báo biến nhận giá trị, hoặc chỉ định cụ thể, hoặc ép kiểu đến một tập hợp nào đó nếu có thể.

Các ví dụ bên dưới chỉ ra một số cách sử dụng với phương thức `Select()`.

**Ví dụ:**

* Chọn xem tất cả

```cs
    Student[] students = new Student[10];

    // thêm các thông tin sinh viên ...

    // trả về danh sách kiểu Student
    IEnumerable<Student> s_arr = students.Select(stu => stu);

    foreach(Student s in s_arr)
    {
        Console.WriteLine(s.Id + "\t" + s.Name);
    }
```

* Chọn lọc thuộc tính để trả về

```cs
    Student[] students = new Student[10];
    // thêm các thông tin sinh viên ...

    // trả về một danh sách các 'Name' kiểu string
    IEnumerable<string> names = students.Select(stu => stu.Name);

    foreach(string name in names)
    {
        Console.WriteLine(name);
    }
```

* Sửa đổi giá trị rồi mới trả về

```cs
    int[] arr = { 1, 2, 3, 4, 5 };

    // trả về bình phương của các phần tử
    IEnumerable<int> sqr_arr = arr.Select(item => item * item);

    foreach(int item in sqr_arr)
    {
        Console.WriteLine(item);
    }
```

* Sử dụng cùng với chỉ số

```cs
    int[] arr = { 1, 2, 3, 4, 5 };

    // cộng các phần tử với chỉ số
    IEnumerable<int> newArr = arr.Select((item, index) => item + index);

    foreach(int item in newArr)
    {
        Console.WriteLine(item);
    }
```
Tóm lại, phương thức `Select()` của LINQ có cách dùng tương tự như lệnh `SELECT` trong SQL. 

Một số phương thức có thể dùng đến để chuyển đổi kiểu tập hợp từ `IEnumerable<T>` sang kiểu tập hợp khác như `ToList()`, `ToHashSet()`, `ToArray()`, `ToDictionary()`, ... đôi khi sẽ trở nên hữu ích.

**Ví dụ:**

```cs
    int[] arr = { 1, 2, 3, 4, 5 };

    int[] newArr = arr.Select(item => item + 1).ToArray();

    foreach(int value in newArr) {
        Console.WriteLine(value);
    }
```

### Phương thức Where()

Phương thức `Where()` được dùng để lọc dữ liệu theo một điều kiện cụ thể nào đó.

Cú pháp:

```cs
    IEnumerable<TSource> Where(Func<TSource, bool> predicate)
    IEnumerable<TSource> Where(Func<TSource, int, bool> predicate)
```

Tham số kiểu `int` đại diện cho chỉ số phần tử được dùng trong các điều kiện có dùng đến chỉ số trong logic.

**Ví dụ:**

```cs
    Student[] students = new Student[10];
    // thêm các thông tin sinh viên ...

    // trả về danh sách sinh viên có điểm lớn hơn 5
    List<Student> gt5Students = students.Where(s => s.Score >= 5.0).ToList();

    foreach(Student s in gt5Students)
    {
        Console.WriteLine(s.Id + "\t" + s.Name + "\t" + s.Score);
    }
```

Các phương thức mở rộng không riêng gì phương thức `Where()` đều có thể kết hợp với nhau (gọi thành chuỗi phương thức) nhằm tạo ra câu lệnh ngắn gọn và tương tự câu truy vấn SQL.

**Ví dụ:**

```cs
    Student[] students = new Student[10];

    // danh sách tên sinh viên có điểm lớn hơn 5
    List<string> gt5StudentNames = students.Where(s => s.Score >= 5.0).Select(s => s.Name).ToList();

    foreach(string name in gt5StudentNames)
    {
        Console.WriteLine(name);
    }
```

Nên xem xét kiểu mà phương thức mở rộng trả về trước khi kết hợp với một phương thức mở rộng khác, và từ đó xem xét thứ tự gọi các phương thức. Đặc biệt là phương thức `Select()` thường được đặt ở cuối (trong đa phần các trường hợp).

**Ví dụ:**

```cs
    var result1 = students.Where(s => s.Score >= 8.0).Select(s => s.Name); // OK

    var result2 = students.Select(s => s.Name).Where(s => s.Score >= 8.0); // lỗi, kiểu 'string' không có thuộc tính 'Score'
```

**Giải thích:** 

* Với `result1`, gọi `Select()` sau khi gọi `Where()`. Phương thức `Where()` lúc này trả về kiểu `IEnumerable<Student>`, và kiểu `Student` thì có thuộc tính `Score`, nên câu lệnh hợp lệ.

* Ngược lại nếu gọi `Select(s => s.Name)`, nó trả về kiểu `IEnumerable<string>`, và kiểu `string` thì không có thuộc tính `Score`. Vì vậy gây ra lỗi biên dịch.

Vì vậy, ta cần quan tâm đến kết quả trả về và thứ tự gọi phương thức.


