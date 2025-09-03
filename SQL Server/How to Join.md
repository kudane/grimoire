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
A JOIN B
→ ได้ชุดชั่วคราว TempA_B

TempA_B LEFT JOIN C
→ ได้ TempA_B_C โดยคงทุกแถวจาก TempA_B

TempA_B_C RIGHT JOIN D
→ ได้ TempA_B_C_D โดยคงทุกแถวจาก D

TempA_B_C_D FULL OUTER JOIN E
→ ได้ TempA_B_C_D_E โดยคงทุกแถวจากทั้ง TempA_B_C_D และ E

สรุป:
Join จะทำงาน “ซ้อน” กันทีละขั้น
คำสั่ง ON แต่ละตัวจะถูกใช้ตอน join นั้นๆ เท่านั้น
ลำดับมีผลสำคัญ ถ้าเปลี่ยนลำดับหรือวงเล็บ ผลลัพธ์อาจไม่เหมือนเดิม
