# Logging ใน .NET

Logging เป็นหัวใจสำคัญของการพัฒนาซอฟต์แวร์ ช่วยให้นักพัฒนาสามารถติดตามการทำงานของแอปพลิเคชัน, ตรวจสอบข้อผิดพลาด (Debug), และวิเคราะห์ปัญหาที่เกิดขึ้นบน Production ได้อย่างรวดเร็ว ใน .NET Ecosystem นั้น `Microsoft.Extensions.Logging` คือไลบรารีมาตรฐานที่ทำหน้าที่เป็น Abstraction Layer ทำให้เราสามารถสลับสับเปลี่ยนผู้ให้บริการ Logging (Provider) ได้โดยไม่ต้องแก้ไขโค้ดหลักของแอปพลิเคชัน

## พื้นฐานและแนวคิดหลัก

### Logging Abstraction

.NET ใช้ `Microsoft.Extensions.Logging` เป็นตัวกลางในการจัดการ Log โดยมีส่วนประกอบหลักคือ:

  * `ILogger<T>`: Interface หลักที่ใช้ในการบันทึก Log ภายในคลาสต่างๆ โดย `T` จะถูกใช้เป็น **Category Name** เพื่อระบุที่มาของ Log
  * `ILoggerFactory`: ทำหน้าที่สร้าง instance ของ `ILogger<T>`

### Log Levels

ระดับความสำคัญของ Log (LogLevel) ใช้เพื่อกรองและจัดลำดับความสำคัญของข้อมูลที่บันทึก เรียงจากละเอียดที่สุดไปถึงร้ายแรงที่สุด:

  * `Trace`: ข้อมูลที่ละเอียดมากที่สุด เช่น ค่าของตัวแปร หรือการทำงานทีละขั้นตอน เหมาะสำหรับใช้ตอน Debug แบบลงลึก
  * `Debug`: ข้อมูลสำหรับการ Debug ระหว่างการพัฒนา ไม่ควรเปิดใช้งานบน Production
  * `Information`: ข้อมูลการทำงานทั่วไปของระบบ เช่น "Starting service...", "Processing request complete."
  * `Warning`: เหตุการณ์ผิดปกติที่อาจก่อให้เกิดปัญหาได้ แต่ระบบยังคงทำงานต่อไปได้ เช่น "Disk space is running low."
  * `Error`: ข้อผิดพลาดที่เกิดขึ้นและทำให้การทำงานบางส่วนล้มเหลว แต่แอปพลิเคชันโดยรวมยังไม่ล่ม
  * `Critical`: ข้อผิดพลาดร้ายแรงที่ส่งผลให้แอปพลิเคชันต้องหยุดทำงาน (Crash)
  * `None`: ปิดการบันทึก Log ทั้งหมด

### Logging Providers

Providers คือตัวกลางที่รับ Log Event จากแอปพลิเคชันแล้วส่งต่อไปยังปลายทาง (Sink/Target) ต่างๆ ผู้ให้บริการพื้นฐานที่มาพร้อม .NET ได้แก่:

  * **Console**: แสดงผล Log บน Console
  * **Debug**: แสดงผลในหน้าต่าง Debug ของ Visual Studio
  * **EventSource / EventLog**: บันทึกลงในระบบ Event ของ Windows
  * **Azure**: ส่ง Log ไปยังบริการของ Azure เช่น Application Insights

นอกจากนี้ยังมี Provider ภายนอกที่ได้รับความนิยมสูง เช่น **Serilog** และ **NLog** ซึ่งมีความสามารถสูงกว่ามาก

-----

## การใช้งาน ILogger\<T\>

`ILogger<T>` ถูกออกแบบมาให้ทำงานร่วมกับ Dependency Injection (DI) Container ได้อย่างลงตัว

### หลักการทำงาน

1.  เมื่อเราขอ `ILogger<SomeService>` จาก DI Container, ระบบจะเรียกใช้ `ILoggerFactory`
2.  `ILoggerFactory` จะสร้าง Logger พร้อมกำหนด **Category Name** ให้เป็นชื่อเต็มของ Type `T` (เช่น `MyApp.Services.SomeService`)
3.  Provider จะใช้ Category Name นี้ในการกรองและจัดรูปแบบ Log ทำให้เราสามารถกำหนด LogLevel แยกตามแต่ละ Namespace หรือ Class ได้

### การ Inject และใช้งาน

DI Container ของ .NET รู้จัก `ILogger<T>` เป็นบริการพื้นฐานอยู่แล้ว จึงไม่จำเป็นต้องลงทะเบียนเอง เราสามารถฉีด (Inject) เข้าไปใน Constructor ของคลาสที่ต้องการได้เลย

**ตัวอย่างโค้ด:**

```csharp
public class UserService
{
    private readonly ILogger<UserService> _logger;

    // ฉีด ILogger<UserService> เข้ามาผ่าน Constructor
    public UserService(ILogger<UserService> logger)
    {
        _logger = logger;
    }

    public void CreateUser(string username)
    {
        // Category ของ Log นี้คือ "YourApp.Services.UserService"
        _logger.LogInformation("Attempting to create user {Username}", username);

        if (string.IsNullOrEmpty(username))
        {
            _logger.LogError("Username cannot be null or empty.");
            return;
        }

        // ... business logic ...

        _logger.LogInformation("User {Username} created successfully", username);
    }
}
```

### การตั้งค่า Log Level ใน `appsettings.json`

เราสามารถควบคุม LogLevel ของแต่ละ Category ได้จากไฟล์คอนฟิก ซึ่งสะดวกอย่างยิ่งในการปรับเปลี่ยนบนแต่ละ Environment (Development, Staging, Production)

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information", // LogLevel พื้นฐานสำหรับทุก Category
      "Microsoft.AspNetCore": "Warning", // Log ของ ASP.NET Core ให้แสดงเฉพาะ Warning ขึ้นไป
      "MyApp.Services.UserService": "Debug" // เปิด Debug เฉพาะ UserService
    }
  }
}
```

-----

## เทคนิคและแนวทางปฏิบัติที่ดี (Best Practices)

### 1\. ใช้ Message Template (Structured Logging)

Structured Logging คือการบันทึก Log ในรูปแบบ Key-Value แทนที่จะเป็นข้อความธรรมดา เพื่อให้ง่ายต่อการค้นหาและวิเคราะห์ในภายหลัง **ควรใช้ Message Template เสมอ** แทนการใช้ String Interpolation (`$""`) เพราะมีประสิทธิภาพดีกว่าและช่วยป้องกันการบันทึกข้อมูลที่ไม่จำเป็นหาก LogLevel นั้นถูกปิดอยู่

✅ **ควรทำ (Good):**

```csharp
// ตัวแปร username จะถูกประมวลผลก็ต่อเมื่อ LogLevel Information เปิดใช้งานอยู่
_logger.LogInformation("Creating user {Username} with ID {UserId}", username, userId);
```

❌ **ไม่ควรทำ (Bad):**

```csharp
// String จะถูกสร้างขึ้นเสมอ ไม่ว่า LogLevel จะเปิดอยู่หรือไม่ ทำให้เปลือง Memory
_logger.LogInformation($"Creating user {username} with ID {userId}");
```

### 2\. การบันทึก Exceptions

เมื่อเกิด Exception ควรส่ง instance ของ Exception เข้าไปในเมธอด `LogError` หรือ `LogCritical` โดยตรง เพื่อให้ Provider บันทึกข้อมูลทั้งหมด เช่น Message, Stack Trace, และ Inner Exception

```csharp
try
{
    // ... code that might throw an exception ...
    throw new InvalidOperationException("Something went wrong");
}
catch (Exception ex)
{
    // ส่ง object 'ex' เข้าไปเป็นพารามิเตอร์ตัวแรก
    _logger.LogError(ex, "An error occurred while processing the order {OrderId}", orderId);
}
```

### 3\. Logging Scope เพื่อเพิ่ม Context

`Logging Scope` คือการสร้าง Context ชั่วคราวเพื่อแนบข้อมูลเพิ่มเติมเข้าไปในทุกๆ Log ที่ถูกบันทึกภายในบล็อก `using` เหมาะสำหรับการเพิ่มข้อมูลที่ใช้ร่วมกัน เช่น `RequestId` หรือ `CorrelationId` โดยไม่ต้องส่งค่าไปทุกเมธอด

**ตัวอย่างการใช้งาน:**

```csharp
public void ProcessOrder(int orderId, string userId)
{
    // สร้าง Scope พร้อมข้อมูลประกอบ
    using (_logger.BeginScope(new Dictionary<string, object>
    {
        ["OrderId"] = orderId,
        ["UserId"] = userId
    }))
    {
        _logger.LogInformation("Order processing started");
        // ...
        _logger.LogInformation("Order validation complete");
    }
    // เมื่อออกจาก using block, scope จะถูกทำลาย
}
```

**ผลลัพธ์บน Console:**

```console
info: YourApp.Services.OrderService[0]
      Order processing started => OrderId=123, UserId=abc
info: YourApp.Services.OrderService[0]
      Order validation complete => OrderId=123, UserId=abc
```

-----

## การติดตาม Request ในระบบ Distributed (Centralized Logging)

ในสถาปัตยกรรมแบบ Microservices ที่ Request หนึ่งอาจวิ่งผ่านหลาย Service การติดตามการทำงานทั้งหมดเป็นเรื่องท้าทายมาก **Correlation ID** คือแนวทางแก้ปัญหานี้ โดยเราจะสร้าง ID ที่ไม่ซ้ำกันขึ้นมาสำหรับแต่ละ Request และส่งต่อ ID นี้ไปในทุกๆ Service ที่เกี่ยวข้อง

เราสามารถใช้ Middleware เพื่อสร้างและแนบ Correlation ID เข้าไปใน Logging Scope โดยอัตโนมัติ

**ตัวอย่าง Middleware:**

```csharp
app.Use(async (context, next) =>
{
    // ดึง CorrelationId จาก Header หากไม่มี ให้สร้างใหม่
    var correlationId = context.Request.Headers["X-Correlation-ID"].FirstOrDefault()
                          ?? Guid.NewGuid().ToString();

    context.Response.Headers["X-Correlation-ID"] = correlationId;

    // ใช้ BeginScope เพื่อให้ทุก Log ใน Request นี้มี CorrelationId ติดไปด้วย
    using (logger.BeginScope(new Dictionary<string, object>
    {
        ["CorrelationId"] = correlationId
    }))
    {
        await next();
    }
});
```

เมื่อส่ง Log ทั้งหมดไปยังระบบ Centralized Logging เช่น **Elasticsearch + Kibana** หรือ **Seq** เราจะสามารถใช้ Correlation ID เพื่อกรองดู Log ทั้งหมดที่เกี่ยวข้องกับ Request นั้นๆ ได้ในที่เดียว

-----

## การใช้ External Providers (Serilog / NLog)

แม้ว่า Provider พื้นฐานจะใช้งานได้ดี แต่ Provider ภายนอกอย่าง **Serilog** และ **NLog** มีความสามารถสูงกว่ามาก เช่น:

  * **Structured Logging ที่สมบูรณ์แบบ**: เขียน Log เป็น JSON ได้ง่าย
  * **Sinks/Targets ที่หลากหลาย**: ส่ง Log ไปได้หลายปลายทางพร้อมกัน เช่น File, Database, Elasticsearch, Seq
  * **Enrichers**: เพิ่มข้อมูล Context ลงใน Log โดยอัตโนมัติ (เช่น MachineName, ThreadId)
  * **Performance**: มีโหมด Asynchronous Logging เพื่อลดผลกระทบต่อประสิทธิภาพของแอปพลิเคชัน
  * **Configuration ที่ยืดหยุ่น**: ตั้งค่าผ่าน Code, JSON, หรือ XML ได้อย่างละเอียด

### ตัวอย่างการตั้งค่า Serilog

Serilog เป็นที่นิยมอย่างสูงเพราะความสามารถด้าน Structured Logging

**1. ติดตั้ง Packages:**

```bash
dotnet add package Serilog.AspNetCore
dotnet add package Serilog.Sinks.Console
dotnet add package Serilog.Sinks.File
dotnet add package Serilog.Formatting.Json
```

**2. ตั้งค่าใน `Program.cs`:**

```csharp
using Serilog;
using Serilog.Formatting.Json;

// --- ตั้งค่า Logger ก่อนสร้าง WebApplicationBuilder ---
Log.Logger = new LoggerConfiguration()
    .MinimumLevel.Debug()
    .Enrich.FromLogContext() // เปิดใช้งานเพื่อดึงข้อมูลจาก Logging Scope
    .Enrich.WithMachineName()
    .Enrich.WithThreadId()
    .WriteTo.Console() // แสดงผลแบบธรรมดาบน Console
    .WriteTo.File(new JsonFormatter(), "logs/log.json", rollingInterval: RollingInterval.Day) // เขียนเป็น JSON ลงไฟล์
    .CreateBootstrapLogger(); // ใช้เป็น Logger ชั่วคราวก่อนที่ Host จะถูกสร้าง

var builder = WebApplication.CreateBuilder(args);

// --- ใช้ Serilog เป็น Logging Provider หลัก ---
builder.Host.UseSerilog();

// ... (ส่วนที่เหลือของ Program.cs)
```

**การใช้ `LogContext` กับ Serilog:**
Serilog มี `LogContext` ที่ทำงานร่วมกับ `Enrich.FromLogContext()` ได้ดีเยี่ยม

```csharp
app.Use(async (context, next) =>
{
    var correlationId = Guid.NewGuid().ToString();
    // เพิ่ม Property เข้าไปใน Context
    using (Serilog.Context.LogContext.PushProperty("CorrelationId", correlationId))
    {
        await next();
    }
});
```

**ผลลัพธ์ Log ในไฟล์ JSON:**

```json
{
  "Timestamp": "2025-08-15T21:30:00.123Z",
  "Level": "Information",
  "MessageTemplate": "User {Username} created successfully",
  "RenderedMessage": "User \"Alice\" created successfully",
  "Properties": {
    "Username": "Alice",
    "CorrelationId": "a1b2c3d4-...",
    "MachineName": "WEB-SRV-01",
    "ThreadId": 10,
    "SourceContext": "MyApp.Services.UserService"
  }
}
```

ข้อมูลที่มีโครงสร้างแบบนี้ทำให้การค้นหาใน Kibana หรือ Seq ทรงพลังอย่างยิ่ง

### ตัวอย่างการค้นหาใน Kibana

เมื่อ Log ถูกส่งไปยัง Elasticsearch เราสามารถใช้ Kibana Query Language (KQL) เพื่อวิเคราะห์ได้

  * **ค้นหา Log ทั้งหมดของ Request เดียว:**
    ```kql
    Properties.CorrelationId : "a1b2c3d4-..."
    ```
  * **ค้นหา Error ทั้งหมดที่เกิดใน `UserService`:**
    ```kql
    Level : "Error" and Properties.SourceContext : "MyApp.Services.UserService"
    ```

ด้วยการวางระบบ Logging ที่ดีตั้งแต่ต้น จะช่วยให้การดูแลรักษาระบบในระยะยาวทำได้ง่ายและมีประสิทธิภาพมากขึ้นอย่างมหาศาล
