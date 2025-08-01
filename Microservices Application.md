🧩 Microservices Application คืออะไร?

คือแนวทางการพัฒนาแอปพลิเคชันโดยแยกระบบออกเป็น บริการขนาดเล็กที่เป็นอิสระต่อกัน (small, autonomous services)
แต่ละบริการจะ:

ทำหน้าที่ของมันเองในขอบเขตที่ชัดเจน (bounded context)

มักรองรับฟีเจอร์หรือความสามารถเฉพาะทางด้านธุรกิจเพียงเรื่องเดียว (single business capability)

พัฒนา แก้ไข ปรับใช้ (deploy) ได้แยกจากกัน


แนวคิดนี้คล้ายกับการเอาหลัก Single Responsibility Principle (SRP) ในระดับโค้ด มาขยายใช้ในระดับสถาปัตยกรรมระบบ


---

✅ ข้อดีของ Microservices

1. แยกอิสระอย่างแท้จริง

บริการแต่ละตัวไม่ผูกติดกัน

ทำให้สามารถเปลี่ยนแปลง พัฒนา หรือ deploy ได้แยกกัน
→ ลดผลกระทบเวลามีการอัปเดต


2. ปรับขนาด (Scaling) อย่างชาญฉลาด

ไม่ต้อง scale ทั้งแอปเหมือน Monolith

เลือก scale เฉพาะ service ที่มีภาระงานเยอะได้ เช่น
‣ บริการแสดงสินค้าในแอปอีคอมเมิร์ซที่โดนเรียกบ่อย
‣ ไม่ต้อง scale ส่วนชำระเงินหรือจัดการคำสั่งซื้อถ้าไม่จำเป็น


3. จัดการความซับซ้อนแบบกระจาย

ระบบใหญ่มักวุ่นวาย → การแยกเป็น microservices ทำให้โฟกัสกับแต่ละเรื่องได้ดีขึ้น

ทีม dev สามารถดูแลคนละ service ได้อย่างอิสระ → พัฒนาขนานกันได้


4. บำรุงรักษาง่าย

เปลี่ยน service เดียวไม่ต้องแตะทั้งระบบ

ลดความเสี่ยงที่จะแก้ feature หนึ่งแล้วกระทบอีก feature หนึ่งแบบใน Monolith


5. เหมาะกับ CI/CD และ DevOps

แต่ละ service สามารถใช้ pipeline แยกกัน

ปล่อย feature ใหม่ได้เร็ว โดยไม่ต้องรอรวมทุกอย่าง


6. รองรับการใช้งาน Container

Microservices เหมาะกับ Docker/Kubernetes มาก

.NET Core ก็รันใน container ได้ดี → สร้างบริการขนาดเล็กหลายตัว + deploy ง่าย



---

🔄 การสื่อสารระหว่างบริการ

การเรียกใช้กันไม่ได้เป็น method call ธรรมดาแบบในโค้ดเดียวกัน

ต้องผ่าน network communication เช่น HTTP, gRPC, หรือ Message Bus

แนวทางที่ใช้บ่อยคือ Asynchronous messaging (event-based architecture)

ใช้ queue/broker เช่น RabbitMQ, Kafka

มีเรื่อง retry, resiliency, eventual consistency ที่ต้องคิดให้ดี




---

⚠️ ข้อเสีย / ความท้าทาย

1. ความซับซ้อนโดยรวมสูงขึ้น

แต่ละ service ต้องดูแลเรื่องของมันเอง เช่น DB, Auth, Monitoring, Logging

การสื่อสารข้าม service = ต้องออกแบบให้ดี ไม่งั้นงงยิ่งกว่าเดิม


2. ต้องมีวินัยเรื่อง infrastructure

เรื่องพวก API Gateway, Load balancing, Distributed tracing, Config management ฯลฯ กลายเป็นเรื่องจำเป็น

ถ้า infra ไม่พร้อม → จะกลายเป็น “distributed Monolith”


3. เหมาะกับระบบที่ใหญ่พอ

ถ้าระบบยังเล็ก → ไป microservices ตั้งแต่แรกอาจเกินความจำเป็น

การ maintain หลาย service ตอนที่คนเขียนมี 2 คน = ฝันร้าย



---

🤔 เมื่อไหร่ควรใช้ Microservices?

ระบบมีขนาดใหญ่ และต้องการแบ่งทีมทำงานแบบแยก domain

มีความต้องการในการ scale เฉพาะจุด

มี DevOps pipeline พร้อม หรือใช้ cloud platform ที่ช่วยจัดการ service ได้ดี

มีความชัดเจนในขอบเขตทางธุรกิจ (bounded context)

พร้อมรับมือกับ cost ด้านความซับซ้อนของระบบ

