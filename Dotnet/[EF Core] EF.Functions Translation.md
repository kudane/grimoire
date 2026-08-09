`EF.Functions` ส่วนใหญ่ถูกออกแบบมาให้เป็น **Provider-Specific** (ต้องติดตั้ง NuGet ของเจ้านั้นๆ ถึงจะเห็นคำสั่ง) ผมได้ทำการรวบรวมคำสั่งของ `EF.Functions` ทั้งหมดที่มีในระดับ Relational, SQL Server Provider และ Oracle Provider อย่างละเอียดครบถ้วนมากขึ้น แบ่งตามหมวดหมู่ดังนี้ครับ

### 1. หมวดหมู่ข้อความและการค้นหา (String & Pattern Matching)

| C# (`EF.Functions`) | SQL Server | Oracle |
| --- | --- | --- |
| `EF.Functions.Like(x.Name, "A%")` | `[Name] LIKE N'A%'` | `"Name" LIKE 'A%'` |
| `EF.Functions.Collation(x.Name, "Thai_CS_AS")` | `[Name] COLLATE Thai_CS_AS` | `"Name" COLLATE Thai_CS_AS` |
| `EF.Functions.DataLength(x.Name)` | `DATALENGTH([Name])` | *(ไม่รองรับ)* |
| `EF.Functions.RegexpLike(x.Name, "^A")` | *(ไม่รองรับ)* | `REGEXP_LIKE("Name", '^A')` |
| `EF.Functions.Contains(x.Name, "word")` | `CONTAINS([Name], N'word')` | `CONTAINS("Name", 'word') > 0` |
| `EF.Functions.FreeText(x.Name, "word")` | `FREETEXT([Name], N'word')` | *(ไม่รองรับ)* |
| `EF.Functions.Contains(x.Name, "w", 1054)` | `CONTAINS(..., N'w', LANGUAGE 1054)` | *(ไม่รองรับ Language ID)* |

---

### 2. หมวดหมู่ความห่างของเวลา (Date & Time Differences)

*(หมายเหตุ: ชุดคำสั่งนี้มาจาก SQL Server Provider หากใช้บน Oracle บางคำสั่ง Provider จะพยายามแปลงเป็น Arithmetic (ลบกันตรงๆ) แต่บางคำสั่งจะ Not Supported)*

| C# (`EF.Functions`) | SQL Server | Oracle |
| --- | --- | --- |
| `EF.Functions.DateDiffYear(s, e)` | `DATEDIFF(year, [s], [e])` | `EXTRACT(YEAR FROM e) - EXTRACT(...)` |
| `EF.Functions.DateDiffMonth(s, e)` | `DATEDIFF(month, [s], [e])` | `MONTHS_BETWEEN(e, s)` |
| `EF.Functions.DateDiffWeek(s, e)` | `DATEDIFF(week, [s], [e])` | *(คำนวณจากจำนวนวันหาร 7)* |
| `EF.Functions.DateDiffDay(s, e)` | `DATEDIFF(day, [s], [e])` | `TRUNC(e) - TRUNC(s)` |
| `EF.Functions.DateDiffHour(s, e)` | `DATEDIFF(hour, [s], [e])` | *(ผลต่างวัน * 24)* |
| `EF.Functions.DateDiffMinute(s, e)` | `DATEDIFF(minute, [s], [e])` | *(ผลต่างวัน * 1440)* |
| `EF.Functions.DateDiffSecond(s, e)` | `DATEDIFF(second, [s], [e])` | *(ผลต่างวัน * 86400)* |
| `EF.Functions.DateDiffMillisecond(s, e)` | `DATEDIFF(millisecond, [s], [e])` | *(ไม่รองรับโดยตรง)* |
| `EF.Functions.DateDiffMicrosecond(s, e)` | `DATEDIFF(microsecond, [s], [e])` | *(ไม่รองรับโดยตรง)* |
| `EF.Functions.DateDiffNanosecond(s, e)` | `DATEDIFF(nanosecond, [s], [e])` | *(ไม่รองรับโดยตรง)* |

---

### 3. หมวดหมู่การสร้างวันที่และการปรับโซนเวลา (Date Constructors & TimeZone)

| C# (`EF.Functions`) | SQL Server | Oracle |
| --- | --- | --- |
| `EF.Functions.DateFromParts(y,m,d)` | `DATEFROMPARTS(y, m, d)` | *(ไม่รองรับ)* |
| `EF.Functions.TimeFromParts(h,m,s,ms,...)` | `TIMEFROMPARTS(h, m, s, ...)` | *(ไม่รองรับ)* |
| `EF.Functions.DateTimeFromParts(...)` | `DATETIMEFROMPARTS(...)` | *(ไม่รองรับ)* |
| `EF.Functions.DateTime2FromParts(...)` | `DATETIME2FROMPARTS(...)` | *(ไม่รองรับ)* |
| `EF.Functions.DateTimeOffsetFromParts(...)` | `DATETIMEOFFSETFROMPARTS(...)` | *(ไม่รองรับ)* |
| `EF.Functions.AtTimeZone(x.Date, "UTC")` | `[Date] AT TIME ZONE 'UTC'` | `AT TIME ZONE 'UTC'` |
| `EF.Functions.AddMonths(x.Date, 2)` | *(ไม่รองรับ)* | `ADD_MONTHS("Date", 2)` |
| `EF.Functions.NextDay(x.Date, "MONDAY")` | *(ไม่รองรับ)* | `NEXT_DAY("Date", 'MONDAY')` |

---

### 4. หมวดหมู่ตรวจสอบประเภทข้อมูลและการแปลงข้อมูล (Type Inspection & Conversion)

| C# (`EF.Functions`) | SQL Server | Oracle |
| --- | --- | --- |
| `EF.Functions.IsDate(x.Text)` | `ISDATE([Text])` | *(ไม่รองรับ)* |
| `EF.Functions.IsNumeric(x.Text)` | `ISNUMERIC([Text])` | *(ไม่รองรับ)* |
| `EF.Functions.ToChar(x.Date, "YYYY")` | *(ไม่รองรับ)* | `TO_CHAR("Date", 'YYYY')` |
| `EF.Functions.ToDate(x.Text, "YYYY-MM")` | *(ไม่รองรับ)* | `TO_DATE("Text", 'YYYY-MM')` |
| `EF.Functions.ToNumber(x.Text)` | *(ไม่รองรับ)* | `TO_NUMBER("Text")` |

---

### 5. หมวดหมู่คณิตศาสตร์และสถิติเชิงลึก (Math & Statistical)

*(สถิติเชิงลึกเหล่านี้ส่วนใหญ่ผูกกับ SQL Server และจะคืนค่าเป็น `double` เสมอ)*

| C# (`EF.Functions`) | SQL Server | Oracle |
| --- | --- | --- |
| `EF.Functions.Random()` | `RAND()` | `DBMS_RANDOM.VALUE` |
| `EF.Functions.StandardDeviationSample(x.Col)` | `STDEV([Col])` | `STDDEV_SAMP("Col")` |
| `EF.Functions.StandardDeviationPopulation(...)` | `STDEVP([Col])` | `STDDEV_POP("Col")` |
| `EF.Functions.VarianceSample(x.Col)` | `VAR([Col])` | `VAR_SAMP("Col")` |
| `EF.Functions.VariancePopulation(x.Col)` | `VARP([Col])` | `VAR_POP("Col")` |

---

### 6. หมวดหมู่ JSON (สำหรับ EF Core เวอร์ชั่นใหม่ๆ)

การคิวรี JSON มีความแตกต่างกันสูงมากระหว่าง Database Engine ส่วนใหญ่ใน C# จะใช้วิธี Navigate property ไปตรงๆ แต่ถ้าใช้ผ่าน `EF.Functions` จะมีดังนี้

| C# (`EF.Functions`) | SQL Server | Oracle |
| --- | --- | --- |
| `EF.Functions.JsonType(x.JsonCol)` | `JSON_VALUE([JsonCol], 'lax $')` | *(ขึ้นอยู่กับเวอร์ชั่น Oracle)* |
| `EF.Functions.JsonContains(x.JsonCol, "X")` | *(ทำงานร่วมกับ `OPENJSON` หรือ `IS NOT NULL`)* | `JSON_EXISTS("JsonCol", '...')` |

---

### 7. หมวดหมู่การแปลงประเภทข้อมูล (Data Type Conversion / Casting)

| C# (LINQ & Methods) | SQL Server | Oracle |
| --- | --- | --- |
| **7.1 การแปลงเป็นข้อความ (To String)** |  |  |
| `x.Number.ToString()` | `CAST([Number] AS nvarchar(max))` *หรือ CONVERT* | `TO_CHAR("Number")` *หรือ CAST...* |
| `Convert.ToString(x.Number)` | `CONVERT(nvarchar(max), [Number])` | `TO_CHAR("Number")` |
| `EF.Functions.ToChar(x.Date, "YYYY")` | *(ไม่รองรับ - Provider จะฟ้อง Error)* | `TO_CHAR("Date", 'YYYY')` |
| **7.2 การแปลงเป็นตัวเลข (To Numeric)** |  |  |
| `Convert.ToInt32(x.Text)` | `CONVERT(int, [Text])` | `TO_NUMBER("Text")` |
| `int.Parse(x.Text)` | `CAST([Text] AS int)` | `TO_NUMBER("Text")` |
| `Convert.ToDecimal(x.Text)` | `CONVERT(decimal(18,2), [Text])` | `TO_NUMBER("Text")` |
| `Convert.ToDouble(x.Text)` | `CONVERT(float, [Text])` | `CAST("Text" AS FLOAT)` |
| `EF.Functions.ToNumber(x.Text)` | *(ไม่รองรับ)* | `TO_NUMBER("Text")` |
| **7.3 การแปลงเป็นวันที่ (To DateTime)** |  |  |
| `Convert.ToDateTime(x.Text)` | `CONVERT(datetime2, [Text])` | `TO_TIMESTAMP("Text")` |
| `DateTime.Parse(x.Text)` | `CAST([Text] AS datetime2)` | `TO_TIMESTAMP("Text")` |
| `EF.Functions.ToDate(x.Text, "YYYY")` | *(ไม่รองรับ)* | `TO_DATE("Text", 'YYYY')` |
| **7.4 การแปลงเป็น Boolean (To Boolean)*** |  |  |
| `Convert.ToBoolean(x.IntVal)` | `CONVERT(bit, [IntVal])` | `CAST("IntVal" AS NUMBER(1))` *(ขึ้นอยู่กับ Mapping)* |
| **7.5 การใช้ Explicit Cast (C# Syntax)** |  |  |
| `(long)x.IntVal` | `CAST([IntVal] AS bigint)` | `CAST("IntVal" AS NUMBER(19,0))` |
| `(double)x.IntVal` | `CAST([IntVal] AS float)` | `CAST("IntVal" AS BINARY_DOUBLE)` |
| `(short)x.IntVal` | `CAST([IntVal] AS smallint)` | `CAST("IntVal" AS NUMBER(5,0))` |
| `(string)x.IntVal` *(ส่วนใหญ่ใช้ ToString)* | `CAST([IntVal] AS nvarchar(max))` | `CAST("IntVal" AS NVARCHAR2(2000))` |
