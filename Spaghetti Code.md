# Spaghetti Code: มุมมองจาก Senior Developer

## คืออะไร?
- โค้ดที่โครงสร้างยุ่งเหยิง เหมือนเส้นสปาเกตตีพันกัน
- Flow ไม่เป็นเส้นตรง, function ยาว, duplicate logic, ใช้ global variable มั่ว
- ทำความเข้าใจและดูแลยากมาก

---

## ทำไมถึงเกลียดมัน?
- **Maintain ยาก:** แก้ bug หรือเพิ่ม feature ต้องไล่โค้ดยากมาก
- **เข้าใจยาก:** แม้แต่คนเขียนเองกลับมาดูก็สับสน
- **Technical debt สูง:** ทำให้ productivity ทีมลดลง
- **Google Case:** ระบบเก่า dl.google.com เป็น spaghetti → ทีม Gopher rewrite ใหม่ใน Go ลดโค้ดและเพิ่ม reliability

---

## สาเหตุหลัก
- เขียนโดยไม่วางแผน ไม่มี refactor
- ขาด coding standard และ code review
- เพิ่ม feature ต่อเนื่องโดยไม่จัดระเบียบ
- ทีมใหม่หรือ dev ยังไม่เชี่ยวชาญ

---

## วิธีแก้ไข
1. **Refactor ทีละนิด** แยก function และ module
2. **เพิ่ม Code Review** และ pair programming
3. **ใช้ static analysis** เพื่อตรวจความซับซ้อน
4. **ฝึก Clean Code และ design patterns**
5. **สร้างวัฒนธรรมคุณภาพ** ให้ทีมกล้า prioritize code quality

---

## TL;DR
- Spaghetti code = โค้ดพันกันยุ่ง เข้าใจยาก และแก้ไขยาก
- เกิดจาก short-cut, ขาด discipline, และไม่มีมาตรฐาน
- Senior devs จะรีบ refactor หรือ rewrite
- ถ้าปล่อยไว้ productivity และ maintainability จะพังในระยะยาว
