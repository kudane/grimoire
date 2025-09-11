ในโลกของการพัฒนาฐานข้อมูล Oracle, **Oracle PACKAGE** คือกล่องรวมโปรแกรมย่อย (procedures, functions) และข้อมูลที่เกี่ยวข้อง ทำให้โค้ดเป็นระบบระเบียบและง่ายต่อการจัดการ โดยประกอบด้วย 2 ส่วนหลักคือ **Header** และ **Body**

-----

### 1\. 📃 Package Specification (Header)

ส่วนนี้คือ **"ส่วนประกาศ"** ทำหน้าที่บอกว่า package นี้ **"มีอะไรให้คนอื่นใช้บ้าง"** มันเปรียบเสมือน **"interface"** ที่มีแค่การประกาศชื่อและรูปแบบการใช้งานของ procedure, function, type, หรือ constant ที่เป็น public เท่านั้น ไม่มีโค้ดการทำงานจริง

**ตัวอย่าง:**

```sql
CREATE OR REPLACE PACKAGE pkg_demo IS
  TYPE t_cursor IS REF CURSOR;
  PROCEDURE add_order(p_id NUMBER, p_name VARCHAR2);
  PROCEDURE list_orders(p_cur OUT t_cursor);
END pkg_demo;
/
```

ตัวอย่างนี้ประกาศว่า package `pkg_demo` มี procedure สองตัวที่สามารถเรียกใช้ได้จากภายนอก

-----

### 2\. 📝 Package Body

ส่วนนี้คือ **"ส่วนของการทำงานจริง"** ที่ใส่โค้ดการทำงานของ procedure และ function ที่ประกาศไว้ใน header นอกจากนี้ยังสามารถมี **private items** (เช่น ตัวแปร, procedure, function) ที่ใช้ได้เฉพาะภายใน body เท่านั้น ทำให้ซ่อนรายละเอียดการทำงานภายในได้ (Encapsulation)

**ตัวอย่าง:**

```sql
CREATE OR REPLACE PACKAGE BODY pkg_demo IS
  -- Private variable
  v_counter NUMBER := 0;

  PROCEDURE add_order(p_id NUMBER, p_name VARCHAR2) IS
  BEGIN
    INSERT INTO orders(order_id, order_name)
    VALUES (p_id, p_name);
    v_counter := v_counter + 1;
  END;

  PROCEDURE list_orders(p_cur OUT t_cursor) IS
  BEGIN
    OPEN p_cur FOR
      SELECT * FROM orders;
  END;

END pkg_demo;
/
```

ในตัวอย่างนี้ มีการสร้างตัวแปร `v_counter` ที่เป็น private และใส่โค้ดการทำงานจริงของ procedure ที่ประกาศไว้ใน header

**สรุป:** **Header** เป็นเหมือน **"คู่มือ"** ที่บอกว่า package ทำอะไรได้ ส่วน **Body** เป็น **"หลังบ้าน"** ที่เก็บโค้ดการทำงานจริงไว้ ทำให้โค้ดเป็นระเบียบและจัดการได้ง่ายขึ้น
