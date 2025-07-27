Parse, don’t validate

https://lexi-lambda.github.io/blog/2019/11/05/parse-don-t-validate/

แค่ validate อย่างเดียวไม่พอ—ควร parse input เพื่อให้ได้ข้อมูลในรูปแบบ type ที่ปลอดภัยตั้งแต่แรก

✅ Validation = ตรวจว่าข้อมูลเข้าข่ายไหม → แต่ยังเป็น raw type เดิม

✅ Parsing = validate + แปลง → ได้ type ใหม่ที่บังคับ invariant ครบสมบูรณ์

### สรุปสั้นๆ
Parsing คือการแปลงข้อมูลจากรูปแบบไม่ชัดเจน (เช่น JSON, string) ให้กลายเป็น type ที่ชัดเจน — ถ้าแปลงไม่ได้ → ล้มเหลวเลย (Error)  

ต่างจาก validation แบบธรรมดา ที่เช็คแบบ if/else แล้วยังเก็บข้อมูล raw อยู่ ทำให้ต้องเช็คซ้ำบ่อย บางทีลืมเช็คก็พัง (aka “shotgun parsing”)  

การ parse ส่งผลให้คุณ preserve information คือเก็บหลักฐานของการเช็คไว้ใน type เลย วิธีนี้ทำให้ downstream ใช้งานโดยไม่ต้องเช็คเพิ่ม  

ยังเป็นแนวคิดเดียวกับ refined types หรือ newtypes ที่สร้าง type ใหม่ เช่น EmailAddress, NonEmptyList ฯลฯ ให้ใช้แทน primitive types เพื่อ “make impossible states impossible”  

'''c#
// 1. Validate แบบเดิม (ยังเป็น string)
void Register(string email) {
    if (!IsValidEmail(email)) throw new ArgumentException("Invalid");
    // ยังคงใช้ string ต่อไป—เสี่ยงลืมเช็ค
}

// 2. Parse → type ใหม่ที่ปลอดภัย
public record EmailAddress(string Value) {
    public static EmailAddress Parse(string raw) {
        if (!IsValidEmail(raw))
            throw new ArgumentException("Invalid email");
        return new EmailAddress(raw);
    }
}

// ฟังก์ชันรับเฉพาะ type ที่ปลอดภัย
void Register(EmailAddress email) {
    // email ถูกต้องแน่นอน, ไม่ต้องตรวจเพิ่ม
}

// การเรียกใช้งาน
Register(new EmailAddress("user@x.com"));       // ✅
Register(EmailAddress.Parse("user@x.com"));      // ✅
Register(EmailAddress.Parse("bad"));             // ❌ exception
Register("user@x.com");                          // ❌ ไม่คอมไพล์
'''
