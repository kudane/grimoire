Names are not type safety

https://lexi-lambda.github.io/blog/2020/11/01/names-are-not-type-safety/

### ทำให้โปรแกรมปลอดภัยจากความผิดพลาดด้าน type

Type safety ต้องมาจาก ระบบ type ของภาษา ไม่ใช่จากชื่อ

ปัญหาคือคนมักพึ่งพาชื่อที่ “เหมือน type” แทนที่จะสร้าง type ที่แท้จริง (เช่น สร้าง newtype UserId = Int แทนการใช้ Int ตรง ๆ)

การใช้ชื่ออย่างเดียวไม่ป้องกันการเอาค่าแบบผิด ๆ มายัดใส่ (เช่น EmailAddress ที่จริง ๆ เป็น String)

### สรุปสั้น:
ตั้งชื่อตัวแปรดีอย่างเดียวไม่ช่วยเรื่อง type safety — ต้องสร้าง type ใหม่จริง ๆ ป้องกันการใช้ผิด

```c#
// ชื่อไม่ช่วยป้องกันอะไร
void SendEmail(string emailAddress) { }

// เรียกผิด type ก็ยังคอมไพล์ผ่าน
SendEmail("1234"); // ผิด แต่ผ่านได้

// ใช้ type ที่ชัดเจน
record EmailAddress(string Value);

void SendEmail(EmailAddress email) { }

// เรียกผิด type จะคอมไพล์ไม่ผ่าน
SendEmail("1234"); // ❌ Compile error
SendEmail(new EmailAddress("user@example.com")); // ✅
'''
