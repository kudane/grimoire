# 3-Layer Architecture

## ภาพรวม
การแบ่งระบบออกเป็น 3 เลเยอร์ช่วยให้ **แยกความรับผิดชอบ** และ **บำรุงรักษาง่าย** โดยแต่ละเลเยอร์ทำงานของตนเอง

---

## 1. Presentation Layer
- **หน้าที่:** รับและตอบกลับ HTTP Request/Response
- **ประกอบด้วย:** Controllers, API Endpoints, UI
- **ความรับผิดชอบ:**  
  - รับ Request → Validate เบื้องต้น  
  - เรียกใช้ Business Logic  
  - ส่ง Response ในรูป JSON/XML
- **ข้อดี:** UI/Frontend สามารถเปลี่ยนโดยไม่กระทบ Business Logic
- **ข้อเสีย:** ถ้าใส่ logic ธุรกิจลงไปจะทำให้โค้ดยุ่งและแก้ยาก

---

## 2. Business Logic Layer (BLL)
- **หน้าที่:** เก็บกฎธุรกิจและการคำนวณทั้งหมด
- **ประกอบด้วย:** Business Services, Use Cases, Utilities Services
- **ความรับผิดชอบ:**  
  - กฎธุรกิจและ Workflow
  - Validation เชิงธุรกิจ
  - ประสานงานระหว่าง Presentation และ Data Access
- **ข้อดี:**  
  - Logic อยู่ส่วนกลาง → ทดสอบง่าย
  - เปลี่ยนกฎธุรกิจโดยไม่กระทบเลเยอร์อื่น
- **ข้อเสีย:**  
  - ถ้าออกแบบไม่ดีอาจบวม (God Service)

---

## 3. Data Access Layer (DAL)
- **หน้าที่:** จัดการฐานข้อมูลและระบบภายนอก
- **ประกอบด้วย:** Repositories, ORM
- **ความรับผิดชอบ:**  
  - Query, CRUD Operation
  - Map ข้อมูลระหว่าง DB กับ Model
- **ข้อดี:**  
  - สามารถเปลี่ยน DB โดยไม่กระทบเลเยอร์อื่น
- **ข้อเสีย:**  
  - ไม่ควรใส่ Business Logic

---

## Diagram

```text
+----------------------+
|  Presentation Layer  |
|  (Controllers, API)  |
+----------------------+
           |
           v
+----------------------+
| Business Logic Layer |
| (Services, Rules)    |
+----------------------+
           |
           v
+----------------------+
|  Data Access Layer   |
| (Repositories, DB)   |
+----------------------+
