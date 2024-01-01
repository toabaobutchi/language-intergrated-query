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

Các phương thức này thường được cung cấp cho các kiểu dẫn xuất của `IEnumerable` và `IEnumerable<T>` như mảng, `List<T>`, ... và được định nghĩa trong lớp tĩnh `Enumerable`. Bên cạnh đó, kiểu `IQueryable<T>` cũng được hỗ trợ các cú pháp LINQ.

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
    Student[] students = new Student[]
    {
        new Student() { Id = 1, Name = "Nguyen Van Binh", Score = 7.5 },
        new Student() { Id = 2, Name = "Le Thi Chi", Score = 5.0 },
        new Student() { Id = 3, Name = "Vo Van Anh", Score = 7.0 },
        new Student() { Id = 4, Name = "Tran Thi Hoa", Score = 3.5 },
        new Student() { Id = 5, Name = "Le Van Quy", Score = 2.0 }
    };

    // trả về danh sách kiểu Student
    IEnumerable<Student> s_arr = students.Select(stu => stu);

    foreach(Student s in s_arr)
    {
        Console.WriteLine(s.Id + "\t" + s.Name);
    }
```

* Chọn lọc thuộc tính để trả về

```cs
    Student[] students = new Student[]
    {
        new Student() { Id = 1, Name = "Nguyen Van Binh", Score = 7.5 },
        new Student() { Id = 2, Name = "Le Thi Chi", Score = 5.0 },
        new Student() { Id = 3, Name = "Vo Van Anh", Score = 7.0 },
        new Student() { Id = 4, Name = "Tran Thi Hoa", Score = 3.5 },
        new Student() { Id = 5, Name = "Le Van Quy", Score = 2.0 }
    };

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
    Student[] students = new Student[]
    {
        new Student() { Id = 1, Name = "Nguyen Van Binh", Score = 7.5 },
        new Student() { Id = 2, Name = "Le Thi Chi", Score = 5.0 },
        new Student() { Id = 3, Name = "Vo Van Anh", Score = 7.0 },
        new Student() { Id = 4, Name = "Tran Thi Hoa", Score = 3.5 },
        new Student() { Id = 5, Name = "Le Van Quy", Score = 2.0 }
    };

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
    Student[] students = new Student[]
    {
        new Student() { Id = 1, Name = "Nguyen Van Binh", Score = 7.5 },
        new Student() { Id = 2, Name = "Le Thi Chi", Score = 5.0 },
        new Student() { Id = 3, Name = "Vo Van Anh", Score = 7.0 },
        new Student() { Id = 4, Name = "Tran Thi Hoa", Score = 3.5 },
        new Student() { Id = 5, Name = "Le Van Quy", Score = 2.0 }
    };

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

Vì vậy, ta cần quan tâm đến kết quả trả về của phương thức LINQ và thứ tự gọi chúng.

### Phương thức OrderBy() và OrderByDescending()

Phương thức này được dùng để sắp xếp một tập hợp tăng dần theo một điều kiện cụ thể nào đó được chỉ định.

Dù được dùng để sắp xếp tăng dần, tuy nhiên ta vẫn có thể sắp xếp giảm dần bằng tham số `IComparer<T>` của phương thức `OrderBy()`.

Cú pháp:

```cs
    IOrderedEnumerable<TSource> OrderBy(Func<TSource,TKey> keySelector)
    IOrderedEnumerable<TSource> OrderBy(Func<TSource,TKey> keySelector, IComparer<Tkey> comparer)
```

Trong đó:

* `Tkey` là kiểu mà ta muốn thực hiện so sánh và sắp xếp. Tham số `keySelector` dùng biểu thức Lambda để chỉ ra thành phần giá trị dùng để so sánh.

* `IOrderedEnumerable` là một sub-interface của `IEnumerable`.

* `IComparer<TKey>` là interface dùng để chỉ ra cách so sánh giữa 2 đối tượng.

Nếu không chỉ định tham số `IComparer<TKey>`, phương thức `OrderBy()` sẽ sắp xếp tăng dần dựa vào đối tượng `IComparer` mặc định của kiểu `TKey` (đa phần các kiểu dữ liệu nguyên thủy bao gồm cả `string` đều có  trình so sánh riêng).

> Xem thêm trình so sánh mặc định [**Comparer<T>.Default**](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.comparer-1.default?view=net-7.0).

**Ví dụ:**

```cs
    Student[] students = new Student[]
    {
        new Student() { Id = 1, Name = "Nguyen Van Binh", Score = 7.5 },
        new Student() { Id = 2, Name = "Le Thi Chi", Score = 5.0 },
        new Student() { Id = 3, Name = "Vo Van Anh", Score = 7.0 },
        new Student() { Id = 4, Name = "Tran Thi Hoa", Score = 3.5 },
        new Student() { Id = 5, Name = "Le Van Quy", Score = 2.0 }
    };

    // sắp xếp theo điểm tăng dần – theo kiểu double
    IEnumerable<Student> orderedStudents = students.OrderBy(s => s.Score);

    foreach(Student s in orderedStudents)
    {
        Console.WriteLine(s.Id + "\t" + s.Name + "\t" + s.Score);
    }
```

Nếu muốn triển khai interface `IComparer<T>`, hãy xem phần [**Triển khai IComparer<T>**]().

> [!Note]
>
> Để sắp xếp giảm dần, ta có thể sử dụng phương thức `OrderByDescending()` với các tham số và cách dùng tương tự phương thức `OrderBy()`.


### Phương thức Union()

Khi muốn kết hợp 2 tập hợp thành một tập hợp duy nhất, **bỏ qua các phần tử trùng lặp** thì ta có thể sử dụng phương thức mở rộng `Union()`.

Cú pháp:

```cs
    IEnumerable<TSource> Union(IEnumerable<TSource> second)
    IEnumerable<TSource> Union(IEnumerable<TSource> second, IEqualityComparer<TSource> comparer)
```

Vì Union() bỏ qua các phần tử trùng nhau và chỉ giữ lại 1 phiên bản, do đó mà phương thức này cần biết cách để so sánh bằng nhau giữa các phần tử. Mặc định nếu không chỉ định trình so sánh (so sánh bằng) `IEqualityComparer<TSource>` thì phương thức sẽ sử dụng trình so sánh mặc định trên kiểu.

**Ví dụ:**

```cs
    int[] a = { 5, 3, 3, 2, 7, 5 };
    int[] b = { 7, 9, 12, 6 };

    IEnumerable<int> union = a.Union(b);

    foreach(int item in union)
    {
        Console.Write(item + " "); // 5 3 2 7 9 12 6
    }
```

Tuy nhiên, đối với các kiểu dữ liệu do lập trình viên định nghĩa, chúng không có trình so sánh bằng giữa 2 đối tượng và ta không thể sử dụng theo cách thông thường. Để sử dụng phương thức `Union()` trên các kiểu dữ liệu tự tạo, ta có 2 cách giải quyết sau:

* Triển khai interface `IEquatable<T>` cho kiểu tự tạo. Phương thức `Union()` sẽ sử dụng phương thức `Equals()` và `GetHashCode()` để thực hiện so sánh bằng và ghép 2 tập hợp.

**Ví dụ:**

```cs
    public class Student : IEquatable<Student>
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public double Score { get; set; }

        public Student(int id, string name, double score)
        {
            Id = id;
            Name = name;
            Score = score;
        }

        // ghi đè phương thức 'Equals(T)' của interface 'IEquatable<T>'
        public bool Equals(Student other) => Id == other.Id;

        // ghi đè các phương thức của kiểu object
        public override bool Equals(object obj)
        {
            Student other = (Student)obj;
            return Id == other.Id;
        }
        public override int GetHashCode() => Id.GetHashCode();
    }

    // sử dụng phương thức Union() trên kiểu Student
    Student[] a = { new Student(1, "John", 6.0), new Student(2, "Mary", 5.5) };
    Student[] b = { new Student(3, "Bob", 7.75), new Student(2, "Mary", 6.0) };

    var union = a.Union(b);

    foreach(var item in union)
    {
        Console.WriteLine(item.Id + ": " + item.Name);
    }
```

* Tạo một lớp triển khai interface `IEqualityComparer<T>` và định nghĩa 2 phương thức `Equals()` và `GetHashCode()`.

```cs
    public class StudentEqualComparer : IEqualityComparer<Student>
    {
        public bool Equals(Student a, Student b) => a.Id == b.Id;
        public int GetHashCode(Student a) => a.Id.GetHashCode();
    }

    // sử dụng phương thức Union() trên kiểu Student
    Student[] a = { new Student(1, "John", 6.0), new Student(2, "Mary", 5.5) };
    Student[] b = { new Student(3, "Bob", 7.75), new Student(2, "Mary", 6.0) };

    var union = a.Union(b, new StudentEqualComparer()); // chỉ định trình so sánh bằng

    foreach(var item in union)
    {
        Console.WriteLine(item.Id + ": " + item.Name);
    }
```

### Phương thức Join()

Phương thức `Join()` được dùng để thực hiện phép kết giữa 2 tập dữ liệu. Các đối tượng trong 2 tập dữ liệu thường có mối quan hệ với nhau như 1 – 1, 1 – N hay N – N.

Phép kết tạo bởi phương thức `Join()` tương tự phép kết `INNER JOIN` của SQL.

Cú pháp:

```cs
    IEnumerable<TResult> Join(
        IEnumerable<TInner> inner,
        Func<TOuter,TKey> outerKeySelector,
        Func<TInner,TKey> innerKeySelector,
        Func<TOuter,TInner,TResult> result
    )

    IEnumerable<TResult> Join(
        IEnumerable<TInner> inner,
        Func<TOuter,TKey> outerKeySelector,
        Func<TInner,TKey> innerKeySelector,
        Func<TOuter,TInner,TResult> result,
        IEqualityComparer<TKey> comparer
    )
```

Trong đó: Gọi **`A`** là tập hợp gọi phương thức `Join()`, **`B`** là tập hợp mà A cần tạo phép kết.

* `inner`: là tập hợp cần thực hiện phép kết.

* `outerKeySelector`: hàm chỉ ra khóa cần tạo phép kết của tập hợp **`A`**.

* `innerKeySelector`: hàm chỉ ra khóa của **`B`** cần tạo phép kết với khóa chỉ ra bởi `outerKeySelector` của **`A`**.

* `result`: thao tác và chọn giá trị sẽ trả về

* `comparer`: chỉ ra cách so sánh bằng giữa `outerKeySelector` và `innerKeySelector`.

**Ví dụ:**

```cs
    class Student {
        public string Name { get; set; }
        public int ClassID { get; set; }
    }

    class Class {
        public int ClassID { get; set; }
        public string ClassName { get; set; }
    }

    // sử dụng phương thức Join()
    List<Student> students = new List<Student> { 
        new Student { Name = "John", ClassID = 3 }, 
        new Student { Name = "Mary", ClassID = 3 },
        new Student { Name = "Bob", ClassID = 2 },
        new Student { Name = "David", ClassID = 1 } // không có lớp nào id = 1
     };

    List<Class> classes = new List<Class> { 
        new Class { ClassID = 2, ClassName = "C2" },
        new Class { ClassID = 3, ClassName = "C3" }
    };

    var results = students.Join(classes, 
                                s => s.ClassID, // khóa của student
                                c => c.ClassID, // khóa của class
                                (s, c) => new { s.Name, c.ClassName } // lấy ra tên sinh viên và tên lớp
    );

    foreach(var item in result)
    {
        Console.WriteLine(item.Sname + " : " + item.Cname);
    }

    /*
        Output:
            John : C3
            Mary : C3
            Bob : C2
    */

```

Như vậy, phép kết với phương thức `Join()` chỉ kết các phần tử của 2 tập hợp khi `outerKeySelector` bằng với `innerKeySelector` (tương tự phép `INNER JOIN` của SQL).

Tìm hiểu thêm về tham số `IEqualityComparer<T>` dùng cho phương thức `Join()` trong trường hợp cần chỉ ra cách so sánh giữa 2 khóa.

### Phương thức Single() và SingleOrDefault()

Phương thức `Single()` được dùng để lấy một phần tử trong tập phần tử hiện tại.

**Ví dụ:**

```cs
    var result = classes.Where(c => c.ClassID == 1).Single();
```

> [!Warning]
> 
> Nếu như tập hợp không trả về chính xác 1 phần tử (không hơn và không kém), phương thức `Single()` sẽ ném ra ngoại lệ `InvalidOperationException`. Do đó, phương thức này thường theo sau mệnh đề `Where()`.

Phương thức `Single()` có thể không cần đi kèm với `Where()` nếu chỉ định một điều kiện tìm với cú pháp:

```cs
    TSource Single<TSource>(Func<TSource, bool> predicate)
```

**Ví dụ:**

```cs
    var result = classes.Where(c => c.ClassID == 1).Single();
    // có thể thay thế như bên dưới
    var result = classes.Single(c => c.ClassID == 1);
```

Phương thức `Single()` sẽ ném ngoại lệ khi tập dữ liệu trả về nhiều hơn một phần tử hoặc không trả phần tử nào. Tuy nhiên, nếu vẫn chấp nhận việc tập hợp **không trả về phần tử nào**, ta có thể sử dụng phương thức `SingleOrDefault()` để thay thế.

Cú pháp:

```cs
    SingleOrDefault<TSource>()
    SingleOrDefault<TSource>(TSource defaultValue)
    SingleOrDefault<TSource>(Func<TSource, bool> predicate)
    SingleOrDefault<TSource>(Func<TSource, bool> predicate, TSource defaultValue)
```

Tham số `defaultValue` sẽ được dùng trả về nếu như tập hợp không trả vềphần tử nào. Nếu không chỉ định tham số này, giá trị `default(TSource)` sẽ được sử dụng.

Về cơ bản, phương thức `SingleOrDefault()` có cách dùng tương tự như phương thức `Single()`.

**Ví dụ:**

```cs
    int[] num = { -5, 6, 13, 54, 16, -9 };

    var r1 = num.Where(n => n % 7 == 0).SingleOrDefault(); // r1 = default(int) = 0

    var r2 = num.SingleOrDefault(n => n % 5 == 0); // r2 = -5

    var r2 = num.Where(n => n < -10).SingleOrDefault(-1); // r2 = -1
```

### Phương thức First() và FirstOrDefault()

Phương thức `First()` và `FirstOrDefault()` được dùng để lấy phần tử đầu tiên từ tập dữ liệu trả về hoặc theo điều kiện lọc cụ thể.

Cú pháp:

* Phương thức `First()`:

```cs
    First<TSource>()
    First<TSource>(Func<TSource, bool> predicate)
```

* Phương thức `FirstOrDefault()`:

```cs
    FirstOrDefault<TSource>()
    FirstOrDefault<TSource>(TSource defaultValue)
    FirstOrDefault<TSource>(Func<TSource, bool> predicate)
    FirstOrDefault<TSource>(Func<TSource, bool> predicate, TSource defaultValue)
```

Phương thức `First()` sẽ ném ra ngoại lệ nếu như tập dữ liệu không chứa phần tử nào. Ta có thể sử dụng `FirstOrDefault()` để dùng giá trị mặc định khi tập dữ liệu rỗng.

**Ví dụ:**

```cs
    int[] nums = { 0, 6, 4, -1, 3, 7, -7 };

	var r1 = nums.Where(n => n < 0).First(); // r1 = -1

	var r2 = nums.First(n => n % 2 == 0); // r2 = 6

	var r3 = nums.FirstOrDefault(n => n % 5 == 0); // r3 = 0
```

### Phương thức Distinct() và DistinctBy()

Nếu muốn lấy ra tập dữ liệu có các phần tử không trùng nhau (có thể theo tiêu chí lọc nào đó), ta có thể sử dụng phương thức `Distinct()` hoặc `DistinctBy()`.

**Ví dụ:**

```cs
    int[] nums = { 1, 4, 5, 3, 7, 3, 2, 1 };

	var distinctNums = nums.Distinct();

	foreach(var value in distinctNums)
    {
		Console.Write(value + " ");
	}
	// output: 1 4 5 3 7 2
```

Nếu như không nhận tham số gì, phương thức `Distinct()` sẽ dùng trình so sánh mặc định của kiểu tập hợp. Nếu muốn sử dụng cho các kiểu dữ liệu tự định nghĩa, hãy triển khai `IEquatable<T>`.

Một phiên bản nạp chồng khác của phương thức `Distinct()` nhận tham số kiểu `IEqualityComparer<TSource>` dùng chỉ ra cách so sánh giữa 2 phần tử nếu cần.
Đối với các kiểu dữ liệu tự định nghĩa, phương thức `DistinctBy()` được sử dụng nhiều hơn với cú pháp:

```cs
    DistinctBy<TSource,TKey>(Func<TSource,TKey> keySelector)
```

Với cú pháp trên, ta có thể dùng biểu thức Lambda để chỉ ra khóa nào sẽ được dùng so sánh giữa các phần tử trong tập dữ liệu.

**Ví dụ:**

```cs
    List<Student> students = new List<Student> { 
        new Student { Name = "John", ClassID = 3 }, 
       	new Student { Name = "Mary", ClassID = 3 },
        new Student { Name = "Bob", ClassID = 2 },
        new Student { Name = "David", ClassID = 1 }
    };
	var result = students.DistinctBy(s => s.ClassID).Select(s => s.ClassID);
	// result = { 3, 2, 1 }
```

Trong trường hợp khóa được chọn không có trình so sánh mặc định, ta có thể chỉ định tham số kiểu `IEqualityComparer<TSource>`.

### Các phương thức khác

Xem chi tiết về các phương thức mở rộng của LINQ tại [**LINQ method syntax**](https://learn.microsoft.com/en-us/dotnet/api/system.linq.enumerable?view=net-7.0).

## Triển khai IComparer<T>

IComparer<T> là interface dùng để chỉ định cách so sánh giữa 2 đối tượng, giá trị.

Các kiểu dữ liệu dựng sẵn đã có sẵn trình so sánh, nhưng với các kiểu dữ liệu phức tạp, ta phải tự định nghĩa các so sánh.

Để triển khai `IComparer<T>`, hãy định nghĩa phương thức `Compare(T x, T y)` trả về kiểu `int` cho biết giá trị tương đối giữa `x` và `y` như sau:

* Nhỏ hơn 0 nếu `x` < `y`

* Bằng 0 nếu `x` = `y`

* Nhỏ hơn 0 nếu `x` > `y`

> [!Note]
>
> Việc so sánh với `null` không gây ra lỗi hoặc ngoại lệ. Giá trị `null` luôn **nhỏ hơn** các giá trị non-null.

**Ví dụ:**

```cs
    public class StudentNameComparer : Comparer<Student>
    {
        public int Compare(Student x, Student y)
        {
            string nameX = x.Name.Substring(x.Name.LastIndexOf(" ")); // lấy ra phần tên của x
            string nameY = x.Name.Substring(x.Name.LastIndexOf(" ")); // lấy ra phần tên của y

            return fnameX.Compare(fnameY);
        }
    }
```
