## ชุดคำสั่ง Attach และ Update

การอัพเดทข้อมูลใน EF Core มีหลายวิธี ซึ่งผลลัพธ์ไม่เหมือนกัน
    db.Update(entity) จะมีการทำงานคือ EF Core มองว่าทุก property ถูกเปลี่ยน
    db.Attach(entity) จะมีการทำงานคือ กำหนด property ที่เปลี่ยนหรือเปลี่ยนแปลงข้อมูลหลังการ Attach, EF Core จะตรวจสอบ property ที่ถูกแก้ไข

## อัพเดทแบบเฉพาะที่ใช้ vs อัพเดททั้งหมด

ตัวอย่างการใช้ Update()
```c#
var user = new User { Id = 1, Phone = "090-111-2222" };
context.Update(user);
context.SaveChanges();

/*
UPDATE [Users]
SET [Name] = @p0, [Email] = @p1, [Phone] = @p2
WHERE [Id] = @p3;
*/
```

ตัวอย่างการใช้ Attach + ระบุ Property/ แก้ไขภายหลัง
```c#
var user = new User { Id = 1, Phone = "090-111-2222" };  // ต้องมี primary key
context.Attach(user);
context.Entry(user).Property(u => u.Phone).IsModified = true;
context.SaveChanges();

/*
UPDATE [Users]
SET [Phone] = @p0
WHERE [Id] = @p1;
*/
```

```c#
var user = context.Users.AsNoTracking().First();
context.Attach(user);
user.Phone = "090-111-9999"; // ข้อมูลใหม่
context.SaveChanges();

/*
UPDATE [Users]
SET [Phone] = @p0
WHERE [Id] = @p1;
*/
```

คำเตือน หากมีตัวที่ถูก Tracking อยู่แล้ว อย่า Attach ตัวใหม่ด้วยคีย์เดียวกัน ให้ย้ายค่าลงตัวเดิม หรือ Detach ตัวเดิมก่อนแล้วจึงแนบตัวใหม่ โดยเข้าใจผลข้างเคียงให้ชัด”
InvalidOperationException ข้อผิดพลาดคือ “cannot be tracked because another instance with the same key is already being tracked”.
บทสรุป

- ใช้ Tracking Query สำหรับงานทั้งโปรเจค ถ้าไม่ต้องการเน้นประสิทธิภาพมาก
- ใช้ Attach สำหรับ partial update สำหรับงานที่ต้องการความเร็วลดโหลดฐานข้อมูล
- ใช้ Attach อย่างระมัดระวัง ควรตรวจสอบ scenario ที่จะใช้ให้รอบคอบ
- อย่าใช้ Update() โดยไม่จำเป็น เพราะจะอัพเดททุก column แม้ไม่เปลี่ยนค่า
