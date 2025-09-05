# Join ทำงานต่อกันอย่างไร

ในกรณีที่ฉันหมายถึงคือ
```sql
select *
from A
join B on ....
left join C on ...
right join D on ...
outer join E on ... 
````

การ join จะทำงานอย่างไร? ฉันเข้าใจเป็นแบบนี้ 
```
A join B                  = TempA_B
TempA_B left join         = TempA_B_C 
TempA_B_C right join      = TempA_B_C_D 
TempA_B_C_D outer join E  = TempA_B_C_D_E 
จับคู่ที่ละ join ไปเรื่อยๆ ตามลำดับ
```

### ถูกต้อง หลักการคือ ประมวลผล join ทีละตัวจากซ้ายไปขวา
```sql
SELECT *
FROM A
JOIN B   ON ...
LEFT JOIN C  ON ...
RIGHT JOIN D ON ...
FULL OUTER JOIN E ON ...
```

### การทำงานคือ
```
A JOIN B
→ ได้ชุดชั่วคราว TempA_B

TempA_B LEFT JOIN C
→ ได้ TempA_B_C โดยคงทุกแถวจาก TempA_B

TempA_B_C RIGHT JOIN D
→ ได้ TempA_B_C_D โดยคงทุกแถวจาก D

TempA_B_C_D FULL OUTER JOIN E
→ ได้ TempA_B_C_D_E โดยคงทุกแถวจากทั้ง TempA_B_C_D และ E
```

สรุป:
Join จะทำงาน “ซ้อน” กันทีละขั้น
คำสั่ง ON แต่ละตัวจะถูกใช้ตอน join นั้นๆ เท่านั้น
ลำดับมีผลสำคัญ ถ้าเปลี่ยนลำดับหรือวงเล็บ ผลลัพธ์อาจไม่เหมือนเดิม

### การประกอบข้อมูล (Combine)

ON Clause จะเชื่อมข้อมูลจากตารางต่าง ๆ เข้าด้วยกันตามเงื่อนไข เช่น

```sql
SELECT e.EmployeeID, e.Name, d.DepartmentName
FROM Employees e
JOIN Departments d
   ON e.DepartmentID = d.DepartmentID;
```

* ตรงนี้ ON ทำหน้าที่ **ประกอบข้อมูล** คือ เอา EmployeeID, Name มาจับคู่กับ DepartmentName โดยใช้ DepartmentID ที่ตรงกัน
* ผลลัพธ์จะได้ “ข้อมูลที่มีบริบทครบ” ซึ่งมาจากสองตาราง

---

### การกรองข้อมูล (Filter)

ON Clause ยังทำหน้าที่กรองข้อมูลไปพร้อมกัน เช่น

```sql
SELECT o.OrderID, o.OrderDate, c.CustomerName
FROM Orders o
LEFT JOIN Customers c
   ON o.CustomerID = c.CustomerID
   AND c.IsActive = 1;
```

* การ JOIN จะเชื่อม Orders เข้ากับ Customers **เฉพาะลูกค้าที่ Active เท่านั้น**
* ถ้าลูกค้าไม่ Active เงื่อนไข ON จะไม่ match ทำให้ค่าจากฝั่ง Customers เป็น NULL

---

**สรุปสั้น:**

* ON = รวมข้อมูลให้เชื่อมกัน (combine)
* ON = กรองข้อมูลตามเงื่อนไขไปด้วย (filter)
