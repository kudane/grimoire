Mapping สำหรับคำสั่ง LINQ ใน EF Core และการแปลงเป็น SQL (Translation) สำหรับ SQL Server และ Oracle โดยรวบรวมคำสั่งที่ใช้จริงในชีวิตประจำวัน แบ่งตามหมวดหมู่การใช้งานเพื่อให้ง่ายต่อการอ้างอิงครับ

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
