DTO ย่อมาจาก Data Transfer Object

DTO คือ วัตถุรับส่งข้อมูลภายในระบบ ถูกใช้ในหลายส่วนหลายเลเยอร์ของระบบและยังรวมถึงวัตถุที่ทำหน้าที่ถ่ายโอนข้อมูลโดยมี
วัตถุประสงค์เฉพาะ อย่างเช่น API Request/Response Objects, MVC ViewModel Objects, Database Query Result Objects, และ Messages (Commands, Events, Queries)

มองดู DTO เหมือนจะไม่มีอะไรมากมายแต่มันแนวคิดที่น่าสนใจจากวีดีโอของ Ardalis ที่พูดถึง "5 Rules For DTOs" ดังนี้
กฎข้อที่ 1: DTOs ไม่ควรมีตรรกะหรือพฤติกรรมใด ๆ
กฎข้อที่ 2: DTOs ไม่ควรบังคับใช้ Encapsulation, DTOs ต้องเปิดเผยข้อมูลทั้งหมดในตัวของมัน
กฎข้อที่ 3: DTOs ควรใช้ Properties และไม่ควรใช้ Fields, สงผลเสียต่อการ Serialization และ Deserialization
กฎข้อที่ 4: ตั้งชื่อ DTOs ตามการใช้งาน เช่น LoginRequest เป็นต้น หลักเลี่ยงชื่อลงท้ายด้วย DTO เป็นทางเลือกสุดท้าย
กฎข้อที่ 5: ประเภทต่อไปนี้ทั้งหมดควรถูกจัดเป็น DTOs เช่น API Request/Response Objects, MVC view model objects, database query result objects, และ messages เช่น commands, events และ queries

เพื่อให้การใช้งาน DTOs อย่างมีประสิทธิภาพการตรวจสอบความถูกต้องเป็นส่วนสำคัญในการจัดการข้อมูลที่ DTOs รับส่ง โดยเฉพาะเมื่อ DTO นั้นเป็น Input จากผู้ใช้ (เช่น API request objects) 
แม้ DTOs จะไม่ควรมีพฤติกรรมหรือตรรกะภายใน (กฎข้อที่ 1) แต่การเพิ่มเติม Validation และการตรวจสอบความถูกต้องโดยกลไกภายนอก(Library For Validation) เป็นแนวทางปฏิบัติที่ดีที่ช่วยให้มั่นใจได้ว่าข้อมูลที่ถูกส่งผ่าน DTOs นั้นเป็นไปตามข้อกำหนด
