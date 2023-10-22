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

Ta có thể thêm nhiều mệnh đề `from` để làm việc trên nhiều tập dữ liệu.

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

Mệnh đề `group` dùng để nhóm các khóa có cùng giá trị thành một đối tượng với kiểu `IGrouping<TKey, TElement>`.

Một cách đơn giản mà nói, đối tượng `IGrouping<TKey, TElement>` là một đối tượng chứa một giá trị khóa (với thuộc tính `Key`) và một tập dữ liệu (có thể có hoặc không có phần tử nào).

Cú pháp

```sql
    group <range-variable> by <group-key>
```

Trong đó:

* `range-variable`: là biến lấy từ mệnh đề `from`.

* `group-key` là tiêu chí gộp, là những giá trị có thể nhóm lại được, các giá trị này thường xuất hiện trùng lặp trong bộ dữ liệu gốc và ta muốn nhóm các đối tượng có cùng các giá trị lặp này lại thành nhóm.

Với mỗi `group-key`, mệnh đề `group` sẽ trả về một đối tượng `IGrouping<TKey, TElement>` gồm khóa (cũng là tiêu chí gộp) và các phần tử thõa mãn tiêu chí đó.

Biểu thức truy vấn sau cùng sẽ trả về đối tượng `IEnumerable<IGrouping<TKey, TElement>>`, tức là một danh sách các nhóm. Vì vậy thông thường các xử lý thao tác với dữ liệu mà `group` trả ra sẽ dùng 2 vòng lặp lồng nhau.

**Ví dụ:**

```cs
    int[] arr = { 5, -4, 3, 6, 8 , 10};
    IEnumerable<IGrouping<bool, int>> results = from value in arr
                                                group value by value % 2 == 0; // nhóm theo giá trị chẵn - lẻ

    foreach(IGrouping<bool, int> group in result)
    {
        Console.WriteLine(group.Key ? "Even numbers: " : "Odd numbers: ");

        foreach(int item in group)
        {
            Console.Write(item + "\t");
        }
        Console.WriteLine();
    }

    /* 
    Output:
        Odd numbers:
        5	3
        Even numbers:
        -4	6	8	10 
    */
```

Để tránh dài dòng, ta nên sử dụng từ khóa `var` để nhận các giá trị trả về cho các biểu thức truy vấn nói chung.

Ta cũng có thể dùng giá trị của một thuộc tính trong tập dữ liệu để gộp nhóm.

**Ví dụ:**

```cs
    class Person
    {
        public string Name { get; set; }
        public string Address { get; set; }
    }

    // gộp nhóm theo địa chỉ, thành phố ...
    Person[] people = {
        new Person { Name = "Dung", Address = "HaNoi" },
        new Person { Name = "An", Address = "DaNang" },
        new Person { Name = "Chi", Address = "HCM" },
        new Person { Name = "Binh", Address = "HCM" },
        new Person { Name = "Tuan", Address = "HaNoi" },
        new Person { Name = "Thinh", Address = "HaNoi" }
    };

    var personByAddress = from person in persons
                          group person by person.Address;

    foreach(var personGroup in personByAddress)
    {
        Console.WriteLine($"Person who lives in {personGroup.Key}: ");
        foreach(var person in personGroup)
        {
            Console.WriteLine("- " + person.Name);
        }
    }

    /*
    Output:
        Person who lives in HaNoi:
        - Dung
        - Tuan
        - Thinh
        Person who lives in DaNang:
        - An
        Person who lives in HCM:
        - Chi
        - Binh
    */
```

Khóa dành cho các nhóm không bị giới hạn bởi kiểu dữ liệu nào.

> [!Warning]
> Phía sau mệnh đề `group` không có mệnh đề khác (kể cả mệnh đề `select`) **trừ khi** sử dụng tiếp tục với từ khóa `into` (xem phần [**Từ khóa into**](#từ-khóa-into)).

### Từ khóa into

Từ khóa `into` dùng để tạo một định danh tạm thời (có thể xem như 1 biến tạm thời) và nhận kết quả trả về của mệnh đề [**`group`**](#mệnh-đề-group), [**`join`**](#mệnh-đề-join) hoặc [**`select`**](#mệnh-đề-select).

Khi sử dụng với `group` và `select`, nó ngăn việc kết thúc biểu thức truy vấn và có thể tiếp tục sử dụng với các mệnh đề khác (cho đến khi sử dụng mệnh đề `select` hay `group` tiếp theo để kết thúc biểu thức). Tên gọi chính thức[^1] của từ khóa `into` trong trường hợp này là *continuation*.

[^1]: Từ *continuation* đã được sử dụng trong các bài viết, bài hướng dẫn hay tài liệu của Microsoft.

Đối với việc sử dụng từ khóa `into` trong mệnh đề `join` sẽ được trình bày chi tiết trong phần [**Mệnh đề `join`**](#mệnh-đề-join). Ở phần này, ta sẽ tập trung vào tác dụng của từ khóa `into` đối với mệnh đề `group` và `select`.

Như đã trình bày, từ khóa `into` dùng để nhận về kết quả của mệnh đề `group`, `join` hoặc `select`. Sau khi dùng từ khóa `into`, biến tạm thời sẽ là thứ duy nhất gọi được ở các mệnh đề phía sau.

Cú pháp:

```sql
    [ select | group | join ] ... into <identifier>
```

**Ví dụ:**

```cs
    int[] arr = { 1, 2, 3, 4, 5, 6 };
    var result = from value in arr
                 group value by value % 2 == 0 into g // chỉ còn biến 'g', hủy các biến thao tác phía trên
                 // select value; // lỗi, biến 'value' không có tầm vực ở đây
                 select g; // hợp lệ
```
Biến tạm thời tạo bởi từ khóa `into` có kiểu dữ liệu trùng với kiểu phần tử từ mệnh đề. Ở ví dụ trên, các phần tử trả ra từ mệnh đề `group` có kiểu `IGrouping<bool, int>`, nên biến `g` sẽ có kiểu `IGrouping<bool, int>`.

Thông thường kiểu dữ liệu của biến tạm của mệnh đề `group` và `select` sẽ như sau:

* `... group A by B into C`: biến `C` sẽ cùng kiểu với `A`.

* `... select A into B`: biến `B` sẽ cùng kiểu với `A`.

Sau khi sử dụng `into`, biểu thức truy vấn được phép tiếp tục với các mệnh đề khác.

**Ví dụ:**

```cs
    Person[] people = {
        new Person { Name = "Dung", Address = "HaNoi" },
        new Person { Name = "An", Address = "DaNang" },
        new Person { Name = "Chi", Address = "HCM" },
        new Person { Name = "Binh", Address = "HCM" },
        new Person { Name = "Tuan", Address = "HaNoi" },
        new Person { Name = "Thinh", Address = "HaNoi" }
    };

    var nameByAddress = from person in people
                        group person by person.Address into pGroup;
                        from p in pGroup // duyệt qua các phần tử của pGroup
                        select p.Name; // chỉ lấy tên, không lấy toàn bộ đối tượng
```

Biến tạm dành cho mệnh đề `group` sẽ có kiểu dữ liệu là `IGrouping<TKey, TElement>`, do đó thường sẽ dùng thêm một mệnh đề `from` nữa để duyệt qua, xử lý, ... các phần tử.

### Mệnh đề join

Mệnh đề `join` được dùng để thể hiện phép kết cho 2 tập hợp. Khác với phép kết giữa các bảng trong cơ sở dữ liệu, phép kết của mệnh đề `join` chỉ yêu cầu mỗi tập dữ liệu có một số giá trị có thể so sánh bằng, từ đó làm khóa để kết hợp lại mà không yêu cầu có mối quan hệ nào.

Cú pháp:

```cs
    join <range-variable> in <join-data-source> on <key 1> equals <key 2>
```

Trong đó:

* `range-variable`: giống như biến dùng với mệnh đề `from`, `group`, ... Tuy nhiên, biến này được dùng để đại diện cho các phần tử trong `join-data-source`.

* `join-data-source`: là tập dữ liệu cần tạo phép kết.

* `key 1`: là khóa để tạo phép kết lấy từ bộ dữ liệu đang dùng (thường lấy từ mệnh đề `from`).

* `key 2`: là khóa lấy từ `range-variable` và cần so khớp với `key 1`.

> [!Note]
> Từ khóa `equals` chỉ dùng trong mệnh đề `join`.

**Ví dụ:**

```cs
    var results = from category in categories
                  join product in products on category.ID equals product.CategoryID
                  select new { ProductName = product.Name, Category = category.Name };
```

Khi sử dụng cùng từ khóa `into`, mệnh đề `join` trở thành **Group join** hoặc **Left outer join**. Nhưng trước tiên, ta sẽ tìm hiểu qua về từ khóa `into` khi sử dụng trong mệnh đề `join`.

---
#### Từ khóa into với mệnh đề join
---

Biến tạm khai báo bởi từ khóa `into` sẽ lưu một đối tượng `IEnumerable<T>` khi dùng với mệnh đề `join`. Với `T` trùng với kiểu tập dữ liệu tạo phép kết. Biến tạm này sẽ lưu danh sách các đối tượng phù hợp với khóa mà tập dữ liệu gốc cung cấp.

**Ví dụ:**

```cs
    var results = from _class in classes
                  join stu in students on _class.ClassID equals stu.ClassID into studentGroup
                  select new { _class.ClassName, Students = studentGroup };

    foreach(var item in results)
    {
        // hiển thị tên lớp
        Console.WriteLine(item.ClassName + ": ");

        // hiển thị tên các sinh viên trong lớp đang xét
        foreach(var student in item.Students)
        {
            Console.WriteLine("\t" + student.Name);
        }
    }
```

Ở ví dụ trên, biến `studentGroup` sẽ lưu đối tượng `IEnumerable<Student>`, là một tập hợp các sinh viên cùng 1 lớp. Ta cũng có thể tiếp tục sử dụng biến `studentGroup` để xử lý và duyệt qua bằng `from`.

**Ví dụ:**

```cs
    var results = from _class in classes
                  join stu in students on _class.ClassID equals stu.ClassID into studentGroup
                  from student in studentGroup // trong mỗi lớp
                  select student.Name + " : " + _class.ClassName;
```

---
#### Group join và Left outer join
---

Những mệnh đề `join` sử dụng từ khóa `into` được gọi là **Group join**.

Bên cạnh đó, **Left outer join** cũng là một dạng của Group join. Left outer join hay Left join là một phép kết cho phép trả về toàn bộ phần tử của tập hợp vế trái mặc dù sẽ có một hoặc một số phần tử không so khớp được với khóa mà tập hợp ở vế phải cung cấp.

Đối với **Inner join** (mệnh đề `join` không sử dụng từ khóa `into`), kết quả trả về sẽ chỉ là những "bộ" dữ liệu tạo được phép kết (khóa A khớp với khóa B). Các phần tử nào không tạo được phép kết sẽ không trả về.

<img src="https://funix.edu.vn/wp-content/uploads/2022/04/innerjoin.png" width="500px"/>

**Ví dụ:**

```cs
    var results = from category in categories
                  join prod in products on category.ID equals prod.CategoryID
                  select new { ProductName = prod.Name, Category = category.Name };

    // Nếu 1 product có 'CategoryID' không trùng với khóa nào của category thì sẽ không được trả về
```

Khi sử dụng từ khóa `into`, một tập hợp sẽ được trả về (dù không có phần tử nào) và ngăn việc tự động bỏ qua đối tượng ở vế trái của biểu thức truy vấn.

![Picture2](https://github.com/toabaobutchi/language-intergrated-query/assets/147165208/01899b7c-fd5f-42fe-ae5d-5097f5f6a0ef)

**Ví dụ:**

```cs
	// left join cho category -> lấy toàn bộ category
	var result = from category in categories
				 join prod in products on category.ID equals prod.CategoryID into prodGroup
				 select new { Category = category.Name, Products = prodGroup };

	foreach(var item in results)
	{
		Console.WriteLine(item.Category);
		foreach(var pro in item.Products)
		{
			Console.WriteLine("\t" + pro.Name);
		}
	}
	// Một số category sẽ không hiển thị sản phẩm nào
```

Như vậy, việc sử dụng từ khóa into giúp ta triển khai được ít nhiều `LEFT JOIN` như SQL.

> [!Warning]
> Nếu như cần xử lý thêm với phép kết bằng mệnh đề `from`, việc sử dụng từ khóa `into` sẽ không đủ để triển khai Left join.

**Ví dụ:**

```cs
	var result = from category in categories
				 join prod in products on category.ID equals prod.CategoryID into prodGroup
				 from p in prodGroup // đôi khi 'prodGroup' không có phần tử nào
				 select new { Category = category.Name, Product = p.Name};
```
Nếu như `propGroup` không có phần tử (trường hợp không so khớp được khóa nào), mệnh đề `from` sẽ bỏ qua các dữ liệu này, và `category` cũng không còn trả về đầy đủ nữa.
Để tránh việc mệnh đề `from` bỏ qua, ta sử dụng phương thức `DefaultIfEmpty()` nhằm chỉ ra phần tử mặc định của vế phải nếu như vế trái không có phần tử nào kết được.

**Ví dụ:**

```cs
	var result = from category in categories
				 join prod in products on category.ID equals prod.CategoryID into prodGroup
				 from p in prodGroup.DefaultIfEmpty()
				 select new { Category = category.Name, Product = p};

	foreach(var item in result)
	{
		Console.WriteLine(item.ToString());
	}
```

Vì trả về một đối tượng mặc định, nên đôi khi ta cần phải kiểm soát và quản lý giá trị `null` nếu như phần tử có kiểu dữ liệu tham chiếu.

Bên cạnh đó, ta cũng có thể truyền vào và chỉ định một đối tượng mặc định cụ thể, ví dụ như sau:

```cs
	... from p in prodGroup.DefaultIfEmpty(new Product { Name = "", Price = 0 }) ...
```

---
#### Composite keys – Mệnh đề join với nhiều khóa
---

Để thực hiện phép kết với nhiều khóa bên A và số lượng khóa tương tự bên B, ta sử dụng cú pháp kiểu ẩn danh. Trường hợp này thường xảy ra khi một đối tượng có nhiều khóa cần kết (có thể hiểu là nhiều khóa chính tương ứng với nhiều khóa ngoại trong SQL).

```cs
	class A
	{
		public int KeyA { get; set; }
		public string KeyB { get; set; }
	}

	class B
	{
		public int KeyA { get; set; }
		public string KeyB { get; set; }
	}
```

Trong trường hợp cần tạo phép kết trên nhiều khóa như trên, ta có thể thực hiện như sau:

```cs
	var results = from a in A_collection
				  join b in B_collection
				  on new { a.KeyA, a.KeyB } equals new { b.KeyA, b.KeyB }
				  ...;
```

Tên của các thuộc tính trong kiểu ẩn danh phải giống nhau để tiến hành so khớp, ở ví dụ trên là thuộc tính `KeyA` của kiểu `A` sẽ so khớp với thuộc tính `KeyA` của kiểu `B`. Nếu không, hãy đặt lại định danh cho chúng.

**Ví dụ:**

```cs
	var result = from a in A
				 join b in B
				 on new { Key1 = a.KeyA, Key2 = a.KeyB }
				 equals new { Key1 = b.KeyC, Key2 = b.KeyD } ...
```

### Mệnh đề let

Xem thêm tại: [**Mệnh đề let**](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/let-clause).
