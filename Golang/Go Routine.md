## การจัดการ dependency
- ไม่มี DI container ในตัว ต้องประกาศและส่ง dependency เอง (ผ่าน parameter, struct field หรือ global variable)
- Lifecycle ของ instance อยู่ที่คุณจัดการเอง (เช่น DBConn ถูกสร้างครั้งเดียวและเก็บใน global var)
- Thread-safety ขึ้นกับ library ที่ใช้ ไม่ได้มี framework คอยบังคับหรือจัดการ

## Thread safety ใน Go
- Go runtime ใช้ goroutine + scheduler จัดการ concurrency
- ตัวแปรที่แชร์กันข้าม goroutine ไม่ได้มี thread safety โดยอัตโนมัติ
- Thread safety ต้องมาจาก
    1. ออกแบบโครงสร้างเองให้ไม่ชนกัน (immutable หรือ local copy)
    2. ใช้ sync.Mutex, sync.RWMutex หรือ channel คุม access
    3. พึ่งพา library ที่ประกาศว่าปลอดภัยข้าม goroutine
- ในกรณี gorm.DB → ใช้ได้พร้อมกันเพราะ GORM ออกแบบให้ thread-safe โดยใช้ connection pool และ lock ภายใน

## ตัวอย่าง สมมุติว่า
- BookService คือ interface เพื่อแยกสัญญาออกจากการ implement
- bookServiceImpl คือ implement จริง เก็บข้อมูลใน memory
- BookHandler คือ struct ที่รับ service ผ่าน constructor (NewBookHandler)
- ใน main() เราสร้าง service แค่ครั้งเดียว (singleton) และส่งไปให้ handler ทุก request ใช้ร่วมกัน

ข้อดี
- แยก business logic ออกจาก HTTP handler ชัดเจน
- ง่ายต่อการเขียน test (mock service ได้)
- คล้าย pattern DI ใน .NET (แต่ Go ไม่มี container มาช่วย ต้องทำเอง)

## ถ้า bookService ตัวเดียวกันถูกแชร์ให้ทุก request เหมือนในตัวอย่าง จะเกิดตามนี้
- Fiber จะสร้าง goroutine แยกต่อ request
- ทุก goroutine จะอ้างถึง pointer เดียวกัน (bookServiceImpl instance เดียว)
- ถ้า service อ่านข้อมูลอย่างเดียว (immutable หรือ read-only) → ปลอดภัย ไม่มี race condition
- ถ้า service แก้ไขข้อมูลภายใน (เช่น append, update, delete ใน slice data) → เสี่ยงเกิด race condition เพราะ goroutine หลายตัวอาจเขียนพร้อมกัน
