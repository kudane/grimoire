
ในภาษา C\#, **unmanaged type** คือชนิดข้อมูลที่หน่วยความจำของมันไม่ได้ถูกจัดการโดย Garbage Collector (GC) ของ .NET โดยตรง ซึ่งหมายความว่า Common Language Runtime (CLR) ไม่ได้ติดตามหรือจัดการการคืนหน่วยความจำของข้อมูลประเภทนี้โดยอัตโนมัติเหมือนข้อมูลประเภทอื่น (ที่เรียกว่า managed types)

การใช้ unmanaged types มีประโยชน์อย่างยิ่งในสถานการณ์ที่ต้องการประสิทธิภาพสูงสุด หรือเมื่อต้องทำงานร่วมกับโค้ดที่ไม่ได้เขียนบน .NET (เช่น library ที่เขียนด้วยภาษา C++) เพราะสามารถเข้าถึงและจัดการหน่วยความจำได้โดยตรง

-----

## \#\# ประเภทของ Unmanaged Type

ชนิดข้อมูลจะถือว่าเป็น **unmanaged type** ได้ก็ต่อเมื่อเป็นหนึ่งในประเภทต่อไปนี้:

  * **ชนิดข้อมูลพื้นฐาน (Primitive Types)**:
      * `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`
      * `nint`, `nuint`
      * `char`
      * `float`, `double`
      * `decimal`
      * `bool`
  * **Enum** ทุกชนิด
  * **Pointer Type** ทุกชนิด (เช่น `int*`)
  * **Struct ที่ผู้ใช้สร้างขึ้นเอง** โดยมีเงื่อนไขสำคัญว่า **ทุก field ภายใน struct นั้นจะต้องเป็น unmanaged type ด้วยกันทั้งหมด**

-----

## \#\# คุณสมบัติสำคัญ

  * **ไม่มี Reference Types**: สิ่งสำคัญที่สุดคือ unmanaged type **จะต้องไม่มี** field ที่เป็น reference type (เช่น `class`, `interface`, `delegate`, `string`, `object`) หรือ field ที่เป็น generic type ที่ไม่ได้ถูกจำกัดว่าเป็น `unmanaged`
  * **ขนาดที่แน่นอน**: เนื่องจาก unmanaged types ประกอบด้วยชนิดข้อมูลที่มีขนาดหน่วยความจำที่แน่นอน ทำให้เราสามารถคำนวณขนาดทั้งหมดของ struct ได้อย่างแม่นยำด้วย `sizeof()` ใน `unsafe` context
  * **การทำงานกับหน่วยความจำโดยตรง**: Unmanaged types มักถูกใช้ใน `unsafe` code เพื่อทำงานกับหน่วยความจำโดยตรงผ่านพอยน์เตอร์ (pointers) ซึ่งให้ประสิทธิภาพที่สูงมาก 🚀
  * **การจัดเก็บในหน่วยความจำ**: โดยทั่วไปแล้ว ตัวแปรที่เป็น unmanaged type จะถูกเก็บไว้ใน **stack** ซึ่งเร็วกว่าการเก็บใน **heap** ที่ใช้สำหรับ managed types

-----

## \#\# ตัวอย่างเปรียบเทียบ

### \#\#\# ตัวอย่าง Unmanaged Struct ✅

`Coords` เป็น unmanaged type เพราะมีแค่ field `X` และ `Y` ซึ่งเป็นชนิด `int` (เป็น unmanaged type)

```csharp
public struct Coords
{
    public int X;
    public int Y;
}
```

### \#\#\# ตัวอย่าง Struct ที่ *ไม่* เป็น Unmanaged ❌

`ManagedCoords` *ไม่เป็น* unmanaged type เพราะมี field `Description` ซึ่งเป็นชนิด `string` (เป็น reference type ซึ่งเป็น managed type)

```csharp
public struct ManagedCoords
{
    public int X;
    public int Y;
    public string Description; // ทำให้ struct นี้กลายเป็น managed type
}
```

-----

## \#\# การใช้งาน

เรามักจะเห็นการใช้ unmanaged type ร่วมกับ `unmanaged` constraint ใน generic เพื่อบังคับว่า type parameter ที่รับเข้ามาต้องเป็น unmanaged type เท่านั้น ซึ่งจำเป็นสำหรับการเขียนโค้ดประสิทธิภาพสูงหรือโค้ดที่ต้องทำงานกับหน่วยความจำในระดับต่ำ

```csharp
public unsafe static void ProcessData<T>(T[] data) where T : unmanaged
{
    // โค้ดที่ทำงานกับหน่วยความจำของ array โดยตรง
    // เพราะเรารู้ว่า T เป็น unmanaged type และมีขนาดที่แน่นอน
    fixed (T* ptr = data)
    {
        // ... สามารถใช้ pointer ptr จัดการข้อมูลใน array ได้
    }
}
```
