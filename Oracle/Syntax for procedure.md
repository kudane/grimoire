# 📘 Oracle PL/SQL Stored Procedure Cheat Sheet

## 1. คำสั่งควบคุมโฟลว์ (Control Flow)

- `IF ... THEN ... ELSE ... END IF;` → เงื่อนไข
- `CASE ... WHEN ... THEN ... END CASE;` → เงื่อนไขหลายแบบ
- `LOOP ... END LOOP;` → loop ไม่รู้จำนวนรอบ (ต้องใช้ `EXIT`)
- `WHILE ... LOOP ... END LOOP;` → loop ตามเงื่อนไข
- `FOR i IN 1..N LOOP ... END LOOP;` → loop จำนวนรอบแน่นอน

---

## 2. Cursor และการวนลูปข้อมูล

- `CURSOR c IS SELECT ...;` → ประกาศ cursor
- `OPEN c; FETCH c INTO var; CLOSE c;` → ใช้ cursor แบบ manual
- `FOR r IN c LOOP ... END LOOP;` → cursor loop แบบสั้น
- `BULK COLLECT INTO` → ดึงหลายแถวทีเดียวใส่ collection

---

## 3. การจัดการ Exception

- `EXCEPTION ... WHEN NO_DATA_FOUND THEN ...` → ดัก error ที่เกิด
- `WHEN OTHERS THEN ...` → ดัก error ทุกกรณี
- `RAISE;` → โยน error เดิมออกไป
- `RAISE custom_exception;` → โยน exception ที่ประกาศเอง

---

## 4. คำสั่ง SQL พื้นฐาน

- `SELECT ... INTO var` → ดึงค่าจาก table มาเก็บในตัวแปร
- `INSERT INTO ... VALUES ...` → เพิ่มข้อมูล
- `UPDATE ... SET ... WHERE ...` → แก้ไขข้อมูล
- `DELETE FROM ... WHERE ...` → ลบข้อมูล
- `MERGE INTO ...` → upsert (insert/update ตามเงื่อนไข)

---

## 5. Dynamic SQL

- `EXECUTE IMMEDIATE 'sql_text' USING ...;` → รัน SQL runtime
- ใช้คู่กับ `USING IN | OUT | IN OUT` → bind parameter

---

## 6. Transaction Control

- `COMMIT;` → ยืนยันการเปลี่ยนแปลง
- `ROLLBACK;` → ยกเลิกการเปลี่ยนแปลง
- `SAVEPOINT name;` → กำหนดจุด rollback
- `ROLLBACK TO name;` → rollback กลับไปที่ savepoint

---

## 7. Utility

- `DBMS_OUTPUT.PUT_LINE('text');` → แสดงข้อความ (debug/logging)
- `DBMS_SQL` → ทำงานกับ dynamic SQL ขั้นสูง
- `DBMS_LOCK.SLEEP(n);` → หน่วงเวลานาน `n` วินาที

---

## 8. ตัวแปรพิเศษ

- `SQL%ROWCOUNT` → จำนวนแถวที่เพิ่งโดน insert/update/delete
- `SQL%FOUND` → true ถ้า statement ล่าสุดมีผล
- `SQL%NOTFOUND` → true ถ้า statement ล่าสุดไม่มีผล
- `cursor_name%ROWTYPE` → ตัวแปรโครงสร้าง row ของ cursor/table

---

## 👉 สรุป
คำสั่งที่เจอบ่อยที่สุดเวลาเขียน procedure คือ  
**`SELECT INTO`, `INSERT/UPDATE/DELETE`, `IF`, `LOOP/FOR`, `EXCEPTION`, `RAISE`, `EXECUTE IMMEDIATE`, `SQL%ROWCOUNT`, `DBMS_OUTPUT.PUT_LINE`**
