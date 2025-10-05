Boxing คือกระบวนการที่ค่า value type (เช่น int, bool, struct) ถูกห่อหุ้มให้อยู่ใน reference type (เช่น object)
Unboxing คือการนำค่าที่ถูกห่อออกมาใช้ใหม่ในรูปแบบ value type เดิม


---

1. ทำงานอย่างไร

เมื่อมีการ boxing ตัว runtime จะจัดสรรพื้นที่ใน heap เพื่อเก็บค่าของ value type แล้วสร้าง reference ไปชี้ตำแหน่งนั้น
เมื่อ unboxing ระบบจะคัดลอกค่าจาก heap กลับไปยัง stack เป็น value type อีกครั้ง

int num = 123;
object obj = num;     // Boxing
int n = (int)obj;     // Unboxing


---

2. โครงสร้างหน่วยความจำ

Boxing: ค่า num เดิมอยู่ใน stack → CLR จะสร้างกล่อง object ใน heap แล้วคัดลอกค่าของมันเข้าไป

Unboxing: CLR ตรวจชนิดของ object ก่อน → ดึงค่ากลับมาที่ stack เป็น value type



---

3. ผลกระทบต่อประสิทธิภาพ

Boxing/Unboxing สร้างภาระ:

มีการจัดสรรหน่วยความจำใน heap

ต้องใช้การแปลงชนิด (casting)

ใช้เวลามากกว่าการจัดการ value type โดยตรง


หลีกเลี่ยงได้โดยใช้ generic หรือ struct แบบตรงชนิด เช่น List<int> แทน ArrayList


---

4. ใช้เมื่อใด

เมื่อจำเป็นต้องจัดเก็บ value type ลงใน container ที่รองรับเฉพาะ object เช่น ArrayList, Hashtable

ใน .NET สมัยใหม่ควรหลีกเลี่ยง boxing โดยใช้ generic collection เช่น List<T>, Dictionary<TKey, TValue>



---

5. สรุป

การทำงาน	จาก	ไป	หน่วยความจำ	ตัวอย่าง

Boxing	Value Type	Object	Stack → Heap	object o = x;
Unboxing	Object	Value Type	Heap → Stack	int y = (int)o;


ข้อมูลเพิ่มเติม: ใช้ generics เพื่อเลี่ยง boxing/unboxing ทั้งเรื่อง performance และความปลอดภัยของชนิดข้อมูล.
