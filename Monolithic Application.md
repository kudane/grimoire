# 🧱 Monolithic Application คืออะไร?

**Monolithic Application** คือแอปพลิเคชันที่รวมทุกส่วนไว้ในก้อนเดียว (single unit)  
ประกอบด้วย **UI**, **Business Logic**, และ **Data Access**  
ทั้งหมดทำงานใน **process เดียว** และมักถูก deploy พร้อมกันเป็นไฟล์เดียว หรือ container เดียว

---

## 🏗️ ลักษณะการทำงาน

- **Deploy ง่าย**: ติดตั้งหรือรันเพียงจุดเดียว เช่น VM, physical server, หรือ container เดียว  
- **โครงสร้างภายใน**: แม้จะเป็น monolith แต่ยังสามารถแบ่งเลเยอร์หรือโฟลเดอร์ได้ เช่น `Models`, `Services`, `Controllers`  
- **จัดการง่ายตอนเริ่ม**: ใช้ IDE เดียว, debug ตรงไปตรงมา, ไม่ต้องสื่อสารข้าม process  
- **เหมาะกับแอปขนาดเล็กถึงกลาง**: ไม่ต้องลงทุนเยอะเรื่อง infra

---

## ⚙️ การปรับใช้และโฮสต์

### Azure Hosting
- **VM / VM Scale Set** → deploy ได้ง่ายเหมือนแอปปกติ  
- **Azure App Service (PaaS)** → ตัวเลือกที่เหมาะที่สุดสำหรับ monolithic web app  

### Scaling
- **Scale up**: เพิ่มสเปกเครื่องเดียวให้แรงขึ้น (CPU, RAM)  
- **Scale out**: คัดลอกทั้งแอปไปรันหลายเครื่อง (load balancing)  
- Azure รองรับการตั้ง **auto-scale** ทั้งสองแบบ  

---

## ✅ ข้อดีของ Monolithic

- **Dev / Debug / Deploy ง่าย**  
- **CI/CD ง่าย**: build/test/release แค่ก้อนเดียว  
- **เหมาะกับ container**: ทำ Docker image เดียวใช้ได้ทุก environment  
- **เริ่มต้นไว**: เหมาะกับ MVP, prototype, หรือระบบที่ยังเล็ก  
- **ประหยัดค่าใช้จ่ายตอนเริ่มต้น**: ไม่ต้องจัดการ network ระหว่าง services  

---

## ❌ ข้อเสียและข้อจำกัด

- **การขยายตัว (scaling) ลำบาก**: อยาก scale แค่บางส่วน → ต้อง scale ทั้งระบบ  
- **Deploy ใหม่ทั้งก้อน**: เปลี่ยนโค้ด component เล็กน้อย → ต้องทดสอบและ deploy ใหม่ทั้งระบบ  
- **จัดการยากเมื่อโต**: โค้ดเบสใหญ่ขึ้นจน maintain ยาก  
- **การ migrate ไป Microservices ยุ่งยาก**: ต้องลงทุนใหม่ทั้งโครงสร้าง, network, monitoring, logging  

---

## 📌 ใช้ Monolithic เมื่อไหร่ดี?

- ระบบยัง **เล็กหรือไม่ซับซ้อน**  
- ทีมถนัด **server-side rendering** มากกว่า SPA/JS-heavy frontend  
- เน้น **SEO** → content ที่โหลดจาก server-friendly สำหรับ search bot  
- ต้องการ **go-to-market เร็ว** → MVP, prototype  
- มีแผนอนาคตที่จะ **refactor ไปเป็น microservices** ได้เมื่อระบบขยาย  

---

## 🔍 เพิ่มเติม (เพื่อให้ครบถ้วน)

- **การทดสอบ (Testing)**: ง่ายกว่าการทดสอบระบบที่มีหลาย service เพราะรันใน process เดียว  
- **Observability**: ง่ายกว่าตอนเริ่มต้น แต่ถ้าโค้ดใหญ่ อาจหาต้นตอของ bug ยากขึ้น  
- **Deployment Pipeline**: ใช้งานง่าย ไม่ต้อง orchestrate หลาย service เหมือน Kubernetes  
