Mapping สำหรับคำสั่ง LINQ ใน EF Core และการแปลงเป็น SQL (Translation) สำหรับ SQL Server และ Oracle โดยรวบรวมคำสั่งที่ใช้จริงในชีวิตประจำวัน แบ่งตามหมวดหมู่การใช้งานเพื่อให้ง่ายต่อการอ้างอิงครับ

### 1. หมวดคิวรี่ (Linq)

| C# (LINQ) | SQL Server | Oracle |
| --- | --- | --- |
| **1. การดึงข้อมูล (Projection & Selection)** |  |  |
| `query.Select(x => x.Name)` | `SELECT [Name]` | `SELECT "Name"` |
| `query.Select(x => new { x.Id, x.Name })` | `SELECT [Id], [Name]` | `SELECT "Id", "Name"` |
| `query.Distinct()` | `SELECT DISTINCT ...` | `SELECT DISTINCT ...` |
| **2. การกรองข้อมูล (Filtering)** |  |  |
| `query.Where(x => x.Age == 18)` | `WHERE [Age] = 18` | `WHERE "Age" = 18` |
| `query.Where(x => x.Age > 18 && x.Age <= 30)` | `WHERE [Age] > 18 AND [Age] <= 30` | `WHERE "Age" > 18 AND "Age" <= 30` |
| `query.Where(x => x.Name == null)` | `WHERE [Name] IS NULL` | `WHERE "Name" IS NULL` |
| `query.Where(x => x.Name != null)` | `WHERE [Name] IS NOT NULL` | `WHERE "Name" IS NOT NULL` |
| `query.Where(x => list.Contains(x.Id))` | `WHERE [Id] IN (...)` | `WHERE "Id" IN (...)` |
| `query.Where(x => x.Name.Contains("A"))` | `WHERE [Name] LIKE N'%A%'` | `WHERE "Name" LIKE '%A%'` |
| `query.Where(x => x.Name.StartsWith("A"))` | `WHERE [Name] LIKE N'A%'` | `WHERE "Name" LIKE 'A%'` |
| `query.Where(x => x.Name.EndsWith("A"))` | `WHERE [Name] LIKE N'%A'` | `WHERE "Name" LIKE '%A'` |
| `query.Where(x => EF.Functions.Like(x.Name, "A_C%"))` | `WHERE [Name] LIKE N'A_C%'` | `WHERE "Name" LIKE 'A_C%'` |
| **3. การจัดเรียง (Sorting)** |  |  |
| `query.OrderBy(x => x.Age)` | `ORDER BY [Age]` | `ORDER BY "Age"` |
| `query.OrderByDescending(x => x.Age)` | `ORDER BY [Age] DESC` | `ORDER BY "Age" DESC` |
| `query.OrderBy(x => x.Age).ThenBy(x => x.Name)` | `ORDER BY [Age], [Name]` | `ORDER BY "Age", "Name"` |
| **4. การแบ่งหน้า (Pagination)** |  |  |
| `query.Take(10)` | `SELECT TOP(10) ...` *(หรือ FETCH NEXT)* | `FETCH FIRST 10 ROWS ONLY` |
| `query.Skip(20).Take(10)` | `OFFSET 20 ROWS FETCH NEXT 10 ROWS ONLY` | `OFFSET 20 ROWS FETCH NEXT 10 ROWS ONLY` |
| **5. การหาค่าทางคณิตศาสตร์ (Aggregation)** |  |  |
| `query.Count()` | `COUNT(*)` | `COUNT(*)` |
| `query.LongCount()` | `COUNT_BIG(*)` | `COUNT(*)` |
| `query.Sum(x => x.Price)` | `SUM([Price])` | `SUM("Price")` |
| `query.Min(x => x.Price)` | `MIN([Price])` | `MIN("Price")` |
| `query.Max(x => x.Price)` | `MAX([Price])` | `MAX("Price")` |
| `query.Average(x => x.Price)` | `AVG(CAST([Price] AS float))` | `AVG("Price")` |
| **6. การจัดกลุ่ม (Grouping)** |  |  |
| `query.GroupBy(x => x.Category)` | `GROUP BY [Category]` | `GROUP BY "Category"` |
| `query.GroupBy(x => new { x.Cat, x.Year })` | `GROUP BY [Cat], [Year]` | `GROUP BY "Cat", "Year"` |
| **7. การดึงข้อมูลแถวเดียว (Single/First row)** |  |  |
| `query.First()` / `FirstOrDefault()` | `TOP(1)` *(มักมาพร้อมกับ ORDER BY)* | `FETCH FIRST 1 ROW ONLY` |
| `query.Single()` / `SingleOrDefault()` | `TOP(2)` *(เพื่อเช็คว่ามีแถวซ้ำหรือไม่)* | `FETCH FIRST 2 ROWS ONLY` |
| `query.Find(id)` | `WHERE [Id] = @id TOP(1)` | `WHERE "Id" = :id FETCH FIRST 1 ROW ONLY` |
| **8. การเชื่อมโยงข้อมูล (Joins & Includes)** |  |  |
| `query.Include(x => x.Orders)` | `LEFT JOIN [Orders] ON ...` | `LEFT JOIN "Orders" ON ...` |
| `query.Join(table2, x=>x.Id, y=>y.XId, ...)` | `INNER JOIN [Table2] ON ...` | `INNER JOIN "Table2" ON ...` |
| `query.SelectMany(x => x.Orders)` | `CROSS JOIN` หรือ `INNER JOIN` | `CROSS JOIN` หรือ `INNER JOIN` |
| `query.GroupJoin(...)` | `LEFT JOIN` (สร้างโครงสร้างแบบ Tree) | `LEFT JOIN` |
| **9. การตรวจสอบเงื่อนไข (Existence / Quantifiers)** |  |  |
| `query.Any()` | `EXISTS (SELECT 1 FROM ...)` | `EXISTS (SELECT 1 FROM ...)` |
| `query.Any(x => x.Age > 18)` | `EXISTS (SELECT 1 FROM ... WHERE Age > 18)` | `EXISTS (SELECT 1 FROM ... WHERE "Age" > 18)` |
| `query.All(x => x.Status == 1)` | `NOT EXISTS (SELECT 1 WHERE Status <> 1)` | `NOT EXISTS (SELECT 1 WHERE "Status" <> 1)` |
| **10. Set Operations** |  |  |
| `query1.Concat(query2)` | `UNION ALL` | `UNION ALL` |
| `query1.Union(query2)` | `UNION` | `UNION` |
| `query1.Intersect(query2)` | `INTERSECT` | `INTERSECT` |
| `query1.Except(query2)` | `EXCEPT` | `MINUS` *(Oracle ใช้ MINUS แทน EXCEPT)* |

---

### 2. หมวดการเชื่อมตาราง (Join Tables)

การ Join ใน EF Core ทำได้ 2 รูปแบบหลักคือ **ผ่าน Navigation Property** (ใช้ `.Include()`) และ **ผ่าน LINQ Join Operator** (เขียนแบบระบุ Key เอง) ในตารางนี้จะเน้นการเขียนแบบระบุ Key เองเพื่อความยืดหยุ่นในการทำเอกสารครับ

| C# (LINQ) | SQL Server (EF Core Translation) | Oracle (EF Core Translation) |
| --- | --- | --- |
| **Inner Join** (เชื่อมแบบมีข้อมูลทั้ง 2 ฝั่ง) |  |  |
| `query1.Join(query2, a => a.Id, b => b.AId, (a, b) => new { a, b })` | `INNER JOIN [Query2] AS [b] ON [a].[Id] = [b].[AId]` | `INNER JOIN "Query2" "b" ON "a"."Id" = "b"."AId"` |
| **Inner Join แบบหลาย Key** (Composite Key) |  |  |
| `query1.Join(query2, a => new { a.K1, a.K2 }, b => new { K1 = b.X1, K2 = b.X2 }, ...)` | `INNER JOIN [Query2] ON [a].[K1] = [b].[X1] AND [a].[K2] = [b].[X2]` | `INNER JOIN "Query2" ON "a"."K1" = "b"."X1" AND "a"."K2" = "b"."X2"` |
| **Left Join** (ดึงข้อมูลฝั่งซ้ายทั้งหมด แม้ฝั่งขวาไม่มีข้อมูล) <br>

<br>*(ใช้ `GroupJoin` คู่กับ `SelectMany` และ `DefaultIfEmpty`)* |  |  |
| `query1.GroupJoin(query2, a => a.Id, b => b.AId, (a, group) => new { a, group }).SelectMany(x => x.group.DefaultIfEmpty(), (x, b) => new { x.a, b })` | `LEFT JOIN [Query2] AS [b] ON [a].[Id] = [b].[AId]` | `LEFT JOIN "Query2" "b" ON "a"."Id" = "b"."AId"` |
| **Cross Join** (จับคู่ทุก Record แบบ Cartesian Product) |  |  |
| `query1.SelectMany(a => query2, (a, b) => new { a, b })` | `CROSS JOIN [Query2] AS [b]` | `CROSS JOIN "Query2" "b"` |
| **Right Join** |  |  |
| *EF Core ไม่มีคำสั่ง Right Join โดยตรง ต้องสลับเอา Query2 ขึ้นก่อนแล้วทำ Left Join แทน* | *N/A (สลับตารางใช้ LEFT JOIN)* | *N/A (สลับตารางใช้ LEFT JOIN)* |

---

### 3. หมวด Subquery (คิวรี่ซ้อนคิวรี่)

EF Core รองรับการเขียน Subquery ในหลายๆ ส่วนของ Statement (Select, From, Where)

| C# (LINQ) | SQL Server (EF Core Translation) | Oracle (EF Core Translation) |
| --- | --- | --- |
| **2.1 Subquery ใน SELECT (Projection)** <br>

<br> *(ดึงค่า Aggregate หรือข้อมูลบางส่วนจากตารางอื่นมาแสดงเป็นคอลัมน์)* |  |  |
| **หาจำนวน (Count) ของตารางลูก**<br>

<br>`db.Authors.Select(a => new { a.Name, BookCount = db.Books.Count(b => b.AuthorId == a.Id) })` | `SELECT [a].[Name], (SELECT COUNT(*) FROM [Books] AS [b] WHERE [b].[AuthorId] = [a].[Id]) AS [BookCount] FROM [Authors] AS [a]` | `SELECT "a"."Name", (SELECT COUNT(*) FROM "Books" "b" WHERE "b"."AuthorId" = "a"."Id") "BookCount" FROM "Authors" "a"` |
| **ดึงข้อมูลแค่ Record เดียวจากตารางลูก (Latest/First)**<br>

<br>`db.Authors.Select(a => new { a.Name, LastBook = db.Books.Where(b => b.AuthorId == a.Id).OrderByDescending(b => b.Year).Select(b => b.Title).FirstOrDefault() })` | `SELECT [a].[Name], [t].[Title] AS [LastBook] FROM [Authors] AS [a] OUTER APPLY (SELECT TOP(1) [b].[Title] FROM [Books] AS [b] WHERE [b].[AuthorId] = [a].[Id] ORDER BY [b].[Year] DESC) AS [t]`<br>

<br>*(EF Core มักใช้ OUTER APPLY แทน Subquery ปกติใน SQL Server)* | `SELECT "a"."Name", "t"."Title" "LastBook" FROM "Authors" "a" LEFT JOIN LATERAL (SELECT "b"."Title" FROM "Books" "b" WHERE "b"."AuthorId" = "a"."Id" ORDER BY "b"."Year" DESC FETCH FIRST 1 ROW ONLY) "t" ON 1 = 1`<br>

<br>*(Oracle 12c+ ใช้ LATERAL)* |
|  |  |  |
| **2.2 Subquery ใน WHERE (Filtering)** <br>

<br> *(ใช้ผลลัพธ์จากคิวรี่อื่นมาเป็นเงื่อนไขในการกรอง)* |  |  |
| **ใช้ EXISTS** (เช็คว่ามีข้อมูลในตารางลูกหรือไม่)<br>

<br>`db.Authors.Where(a => db.Books.Any(b => b.AuthorId == a.Id && b.Price > 500))` | `WHERE EXISTS (SELECT 1 FROM [Books] AS [b] WHERE [b].[AuthorId] = [a].[Id] AND [b].[Price] > 500)` | `WHERE EXISTS (SELECT 1 FROM "Books" "b" WHERE "b"."AuthorId" = "a"."Id" AND "b"."Price" > 500)` |
| **ใช้ NOT EXISTS**<br>

<br>`db.Authors.Where(a => !db.Books.Any(b => b.AuthorId == a.Id))` | `WHERE NOT EXISTS (SELECT 1 FROM [Books] AS [b] WHERE [b].[AuthorId] = [a].[Id])` | `WHERE NOT EXISTS (SELECT 1 FROM "Books" "b" WHERE "b"."AuthorId" = "a"."Id")` |
| **ใช้ IN (Subquery)**<br>

<br>`var subQ = db.Books.Where(b => b.Price > 500).Select(b => b.AuthorId);`<br>

<br>`db.Authors.Where(a => subQ.Contains(a.Id))` | `WHERE [a].[Id] IN (SELECT [b].[AuthorId] FROM [Books] AS [b] WHERE [b].[Price] > 500)` | `WHERE "a"."Id" IN (SELECT "b"."AuthorId" FROM "Books" "b" WHERE "b"."Price" > 500)` |
| **ใช้เทียบกับค่า Scalar (เช่น Max, Min, Avg)**<br>

<br>`db.Books.Where(b => b.Price == db.Books.Max(x => x.Price))` | `WHERE [b].[Price] = (SELECT MAX([x].[Price]) FROM [Books] AS [x])` | `WHERE "b"."Price" = (SELECT MAX("x"."Price") FROM "Books" "x")` |
|  |  |  |
| **2.3 Subquery ใน FROM (Derived Tables / Inline Views)** <br>

<br> *(สร้าง Query ซ้อนทับผลลัพธ์ของ Query ก่อนหน้า EF Core มักสร้าง Subquery เมื่อมีการ Grouping แล้ว Select ต่อ)* |  |  |
| **นำผลการ Group มาค้นหาต่อ**<br>

<br>`var grouped = db.Books.GroupBy(b => b.AuthorId).Select(g => new { AuthorId = g.Key, Total = g.Sum(x => x.Price) });`<br>

<br>`grouped.Where(g => g.Total > 1000)` | `SELECT [t].[AuthorId], [t].[Total] FROM ( SELECT [b].[AuthorId], SUM([b].[Price]) AS [Total] FROM [Books] AS [b] GROUP BY [b].[AuthorId] ) AS [t] WHERE [t].[Total] > 1000` | `SELECT "t"."AuthorId", "t"."Total" FROM ( SELECT "b"."AuthorId", SUM("b"."Price") "Total" FROM "Books" "b" GROUP BY "b"."AuthorId" ) "t" WHERE "t"."Total" > 1000` |
| **ดึงบางส่วนแล้วนำไป Join ตารางหลัก**<br>

<br>`var topBooks = db.Books.OrderByDescending(b => b.Rating).Take(10);`<br>

<br>`topBooks.Join(db.Authors, b => b.AuthorId, a => a.Id, ...)` | `SELECT ... FROM ( SELECT TOP(10) [b].* FROM [Books] AS [b] ORDER BY [b].[Rating] DESC ) AS [t] INNER JOIN [Authors] AS [a] ON [t].[AuthorId] = [a].[Id]` | `SELECT ... FROM ( SELECT "b".* FROM "Books" "b" ORDER BY "b"."Rating" DESC FETCH FIRST 10 ROWS ONLY ) "t" INNER JOIN "Authors" "a" ON "t"."AuthorId" = "a"."Id"` |
