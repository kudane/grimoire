เมื่อรันโปรแกรม C# โค้ดจะถูกคอมไพล์เป็น IL (Intermediate Language) และรันด้วย CLR (Common Language Runtime) ซึ่งทำหน้าที่บริหารหน่วยความจำอัตโนมัติ หน่วยความจำหลักแบ่งเป็น 2 พื้นที่หลัก
    Stack: ใช้เก็บข้อมูลที่มีอายุสั้น เช่น local variables, parameters, return address
    Heap: ใช้เก็บ object หรือ reference types ที่มีอายุยาวกว่า

CLR ยังมี Garbage Collector (GC) ที่จัดการลบ object ที่ไม่ถูกอ้างอิงแล้ว


## Heap ทำงานอย่างไร

    Heap ใช้เก็บข้อมูลประเภท reference type เช่น class, array, string
    ทุกครั้งที่ใช้ new จะมีการจอง memory บน Heap
    ข้อมูลบน Heap จะถูกเข้าถึงผ่าน reference pointer ที่อยู่ใน Stack
    การเคลียร์หน่วยความจำบน Heap เกิดจาก Garbage Collector (GC) ที่ทำงานแบบอัตโนมัติ โดยใช้เทคนิค Mark-and-Sweep หรือ Generation-based Collection

```c#
class Person { public string Name { get; set; } }

void Example() {
    Person p = new Person(); // p อยู่บน Stack, object Person อยู่บน Heap
}
```

## Stack ทำงานอย่างไร

    Stack เป็น memory ที่ทำงานแบบ LIFO (Last In First Out)
    ใช้เก็บ local variables, parameters และ return address ของ method
    ตัวแปร value type (int, bool, struct) จะถูกเก็บตรงบน Stack
    เมื่อ method ทำงานเสร็จ ตัวแปรบน Stack จะถูกลบออกโดยอัตโนมัติ

```c#
void Add(int a, int b) {
    int result = a + b; // a, b, result ถูกเก็บใน Stack
}
```

##Heap และ Stack เกี่ยวข้องกันไหม

ทั้งสองเกี่ยวข้องกันโดยตรง
    เมื่อสร้าง object (new) CLR จะจองพื้นที่ใน Heap และเก็บ reference pointer ของ object ไว้ใน Stack
    Stack จะควบคุม scope และ lifetime ของ reference แต่ Heap จะควบคุม เนื้อหาจริงของ object
    เมื่อ reference บน Stack หมดอายุและไม่มีตัวอื่นอ้างอิง object ใน Heap อีก GC จะลบออก

ภาพรวม:
    Stack = เร็ว, เก็บข้อมูลอายุสั้น
    Heap = ยืดหยุ่น, เก็บ object ที่ซับซ้อน
