# LINQ Query Expression

## Giới thiệu về biểu thức truy vấn

**Biểu thức truy vấn** (Query Expression) là một dạng câu lệnh truy vấn, một phần của LINQ. Biểu thức truy vấn LINQ bao gồm nhiều mệnh đề khiến biểu thức trở nên rất giống với các câu truy vấn SQL.

Ở phần [**LINQ Method Syntax**], ta đã tìm hiểu về phương thức mở rộng (Extension Methods), cũng là một phần của LINQ và được gọi là *method syntax* (tạm dịch: cú pháp dùng phương thức).

Trong nội dung này, ta sẽ tìm hiểu về biểu thức truy vấn, hay còn được biết đến là *query syntax* (tạm dịch: cú pháp truy vấn).

Về mặt ngữ nghĩa, việc sử dụng *method syntax* hay *query syntax* đều giống nhau.

Để đơn giản, ta sẽ chỉ tìm hiểu biểu thức truy vấn ở mức LINQ to Objects, hay trên các tập hợp trong C#.

## Sử dụng biểu thức truy vấn

Như đã đề cập ở phần trên, một biểu thức truy vấn LINQ bao gồm nhiều mệnh đề như `from`, `select`, `where`, ... Các mệnh đề này được xem là các *từ khóa truy vấn* (Query Keywords).

> [!Important]
> Một biểu thức truy vấn phải bắt đầu bằng mệnh đề `from` và kết thúc bằng mệnh đề [**`select`**](#mệnh-đề-select) hoặc [**`group`**](#mệnh-đề-group). Giữa mệnh đề bắt đầu và kết thúc, ta có thể chèn vào các mệnh đề tùy chọn khác như `where`, `orderby`, `join`, ... thậm chí là một mệnh đề `from` khác.

### Mệnh đề from

Một biểu thức truy vấn bắt buộc phải bắt đầu bằng mệnh đề `from`. Bên cạnh đó, mệnh đề `from` có thể dùng để bắt đầu các truy vấn con (Sub-queries).

Tổng quan, mệnh đề `from` được dùng để:

* Chỉ định tập dữ liệu sẽ thao tác.

* Biến đại diện cho từng phần tử trong tập dữ liệu.

Cú pháp:
```sql
    from [datatype] <range-variable> in <data-source>
```
Trong đó:

* `datatype` là kiểu dữ liệu của một phần tử trong tập hợp.

* `range-variable`: biến đại diện cho 1 phần tử đang thao tác.

* `data-source`: tập dữ liệu nguồn.

Cú pháp của mệnh đề `from` khá giống với vòng lặp `foreach`.

Ta có thể bỏ qua việc khai báo `datatype` nếu như `data-source` có kiểu triển khai `IEnumerable<T>`. Trình biên dịch sẽ tự động suy ra nó.

Tuy nhiên, nếu `data-source` chỉ triển khai `IEnumerable` (kiểu non-generic tiêu biểu như `ArrayList`) thì bắt buộc phải chỉ định thêm `datatype`.

> [!Note]
> Mảng một chiều có triển khai cả `IEnumerable` và `IEnumerable<T>`.

**Ví dụ:**

```cs
    Student[] students = {
        new Student(1, "John", 5.5),
        new Student(2, "Mary", 5),
        new Student(3, "David", 7.5),
        new Student(4, "Kelvin", 6.25),
        new Student(5, "Bob", 8.0),
    };

    var names = from student in students 
                select student.Name; // lấy danh sách tên của các sinh viên

    foreach (var name in names)
    {
        Console.WriteLine(name);
    }
```

Ở mệnh đề `from` phía trên, ta có thể khai báo kiểu dữ liệu cho biến cần thao tác như sau:

```sql
    from Student student in students ...
```

Tuy nhiên, trình biên dịch cũng sẽ tự động suy ra kiểu dữ liệu nếu như ta không khai báo gì.

Trong trường hợp với mỗi đối tượng có thêm một tập hợp khác (giả sử với mỗi sinh viên có 1 danh sách môn học, điểm, ...) thì có thể sử dụng thêm các mệnh đề `from` để duyệt qua các tập hợp đó.

```cs
    class Student
    {
        public string Name { get; set; }
        public List<double> Scores { get; set; }
        // các thành viên khác ...
    }

    // giả sử ta có một mảng Student[] students
    // có thể dùng truy vấn như sau để duyệt qua danh sách điểm 'Scores':
    var results = from student in students
                  from score in student.Scores // duyệt từng điểm của sinh viên
                  select ...;
```

Ta có thể thêm nhiều mệnh đề from để làm việc trên nhiều tập dữ liệu.

**Ví dụ:**

```cs
    int[] a = { 1, 2 };
    int[] b = { 1, 2 };
    var results = from ia in a
                  from ib in b
                  select new { ia, ib };

	foreach(var x in results)
    {
		Console.WriteLine(x.ia + "\t" + x.ib);
	}

	/*
    Output:
	    1 1
	    1 2
	    2 1
	    2 2
    */
```

Tóm lại, các mệnh đề `from` lồng nhau sẽ có hành vi tương tự như các vòng lặp lồng nhau.

### Mệnh đề select

Mệnh đề `select` dùng để xác định (chọn) các thành phần, giá trị, thuộc tính nào sẽ trả về qua biểu thức truy vấn. Trong tập hợp đó, mỗi phần tử sẽ có các giá trị mà `select` chỉ định.

Một biểu thức truy vấn phải kết thúc bằng mệnh đề `select` hoặc [**`group`**](#mệnh-đề-group).

Cú pháp:

```cs
    select <object>
```

**Ví dụ:**

```cs
    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public decimal Salary { get; set; }
        // các thành viên khác ...
    }

    // sử dụng mệnh đề select chọn ra tên của nhân viên
    Employee[] employs = { 
        new Employee { Id = 1, Name = "David" },
        new Employee { Id = 2, Name = "Bob" },
        new Employee { Id = 3, Name = "Ban" }
    };

    var names = from e in employs
                select e.Name; // lấy danh sách tên nhân viên

    foreach(var name in names)
    {
        Console.WriteLine(name);
    }
```

Do mệnh đề `select` không thể chọn nhiều giá trị để trả về tập hợp, vì vậy ta thường trả về đối tượng với kiểu ẩn danh (Anonymous Type) với từ khóa `new`.

**Ví dụ:**

```cs
    ... select new { e.Id, e.Name }; // trả về đối tượng ẩn danh gồm id và name
```

Các biểu thức truy vấn LINQ đều trả về giá trị kiểu `IEnumerable<T>`, tuy nhiên khi dùng kiểu ẩn danh để trả về thông qua `select`, ta nên sử dụng từ khóa `var`.


### Mệnh đề where

Mệnh đề `where` dùng để chỉ định phần tử nào sẽ trả về với biểu thức truy vấn. Các phần tử thõa mãn điều kiện đưa ra bởi mệnh đề `where` sẽ được trả về.

Một biểu thức truy vấn có thể chứa nhiều mệnh đề `where` và được thực hiện xét tuần tự.

Cú pháp:

```sql
    where <condition>
```
Trong đó, `condition` là biểu thức điều kiện dùng để lọc. Biểu thức điều kiện là biểu thức trả về kiểu `bool`, có thể chứa nhiều điều kiện con và kết hợp bằng toán tử `&&` hay `||`.

**Ví dụ:**

```cs
    int[] a = { 4, 2, -5, 6, 7, 14, 81 };
    var eNums = from num in a 
                where num % 2 == 0 && num <= 10 // lọc các số chẵn nhỏ hơn 10
                select num;

    foreach(var value in evenNums)
    {
        Console.Write(value + " "); // 4 2 6 14
    }
```

Khi có nhiều mệnh đề `where`, các điều kiện sẽ được cộng dồn lại và phạm vi lọc hẹp hơn.

### Mệnh đề orderby

Mệnh đề này dùng để sắp xếp một tập dữ liệu trả về tăng hoặc giảm dần theo một hoặc nhiều khóa, tiêu chí.

Cú pháp:

```cs
    orderby <key> [ ascending | descending ]
```

Trong đó, `key` là tiêu chí dành để sắp xếp.

Mặc định, mệnh đề `orderby` sẽ sắp xếp tăng dần (tương ứng với `ascending`) và sử dụng trình so sánh mặc định của tiêu chí được chỉ định. Do đó, tiêu chí sắp xếp chỉ có thể là các kiểu dữ liệu có thể so sánh có triển khai `IComparable<T>`.

**Ví dụ:**

```cs
    string[] fruits = { "cherry", "apple", "blueberry" };
    var sortAscendingQuery = from fruit in fruits
                             orderby fruit // mặc định sẽ sắp xếp tăng dần
                             select fruit;

    var sortDescendingQuery = from fruit in fruits
                              orderby fruit descending // sắp xếp giảm dần
                              select fruit;
```

Thông thường, ta thường sắp xếp theo một khóa (thuộc tính) nào đó nhất định, và các khóa này thường thuộc các kiểu dữ liệu có thể sắp xếp như `int`, `string`, ...

Nhưng đối với các khóa là đối tượng, không triển khai `IComparable<T>` thì không thể dùng trong mệnh đề `orderby`. Lúc này hãy sử dụng đến phương thức mở rộng `OrderBy()` hoặc `OrderByDescending()` và cung cấp cách so sánh với tham số `IComparer<T>`.

### Mệnh đề group



