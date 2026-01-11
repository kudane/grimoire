การ Custom ในส่วนของ **Model Binding** และ **Input Formatters** สามารถทำได้หลายระดับตามความต้องการครับ โดยแบ่งออกเป็น **5 วิธีหลัก** ดังนี้:

---

### 1. Custom Input Formatter (จัดการที่ Body)

วิธีนี้ใช้เมื่อคุณต้องการรองรับ **Content-Type ใหม่ๆ** ที่ .NET ไม่ได้เตรียมไว้ให้ (ปกติมีแค่ JSON/XML) เช่น ต้องการรับข้อมูลเป็น `text/csv` หรือรูปแบบ Binary เฉพาะขององค์กร

* **วิธีทำ:** สร้าง Class ที่สืบทอดจาก `InputFormatter` หรือ `TextInputFormatter`
* **การใช้งาน:** ลงทะเบียนใน `Program.cs` ผ่าน `options.InputFormatters.Add(new MyCsvFormatter())`
* **เหมาะสำหรับ:** การเปลี่ยน "วิธีอ่าน Body" ทั้งหมด

---

### 2. Custom Model Binder (จัดการที่ Parameter)

ใช้เมื่อคุณต้องการควบคุม Logic การดึงข้อมูลจากแหล่งต่างๆ (Query, Route, Form) มาใส่ใน Object ของคุณเองแบบละเอียด เช่น การถอดรหัส (Decrypt) ID ที่ส่งมาใน URL ก่อนจะส่งให้ Action

* **วิธีทำ:** สร้าง Class ที่ Implement `IModelBinder`
* **การใช้งาน:** * ใช้ Attribute `[ModelBinder(typeof(MyCustomBinder))]` แปะที่ Parameter
* หรือสร้าง `IModelBinderProvider` เพื่อให้มีผลกับ Type นั้นๆ ทั้งระบบ (Global)


* **เหมาะสำหรับ:** Logic การ Binding ที่ซับซ้อน หรือต้องการดึงข้อมูลจากหลายแหล่งมารวมกัน

---

### 3. TypeConverter (จัดการที่ระดับ Type)

หากคุณมี Custom Type (เช่น `struct` หรือ `class` เล็กๆ) และต้องการให้ .NET แปลงค่าจาก `string` (ที่มาจาก Query หรือ Route) เข้ามาที่ Type นี้ได้โดยตรง

* **วิธีทำ:** สร้าง Class สืบทอดจาก `TypeConverter`
* **การใช้งาน:** แปะ Attribute `[TypeConverter(typeof(MyTypeConverter))]` ไว้บนหัว Class ของ Data Model นั้นๆ
* **เหมาะสำหรับ:** การแปลงข้อมูลพื้นฐาน เช่น แปลงพิกัด `"13.75,100.50"` ให้เป็น Object `Location { Lat, Lon }`

---

### 4. Custom Value Provider (จัดการแหล่งที่มาของข้อมูล)

ปกติ .NET จะดูข้อมูลจาก Form, Route, Query, Header หากคุณต้องการให้ .NET ไปดึงข้อมูลจากแหล่งอื่น เช่น จาก **Cookie** หรือ **Claim** ใน Token โดยอัตโนมัติ

* **วิธีทำ:** Implement `IValueProvider` และ `IValueProviderFactory`
* **การใช้งาน:** ลงทะเบียนใน `options.ValueProviderFactories.Add(...)`
* **เหมาะสำหรับ:** การเพิ่ม "แหล่งที่มา" ของข้อมูลใหม่ๆ

---

### 5. Custom Metadata Provider (จัดการ Metadata)

ใช้เพื่อเปลี่ยนวิธีการที่ .NET "มอง" Model ของคุณ เช่น การสร้างกฎ Validation แบบ Dynamic หรือการเปลี่ยนชื่อ Property ที่จะใช้แสดงผล

* **วิธีทำ:** Implement `IDisplayMetadataProvider` หรือ `IValidationMetadataProvider`
* **เหมาะสำหรับ:** งานขั้นสูงที่ต้องการแก้พฤติกรรมของ Framework ในการอ่าน Attributes ต่างๆ

---

### สรุปการเลือกใช้

| วิธีการ | จุดที่ทำงาน | โจทย์ที่พบบ่อย |
| --- | --- | --- |
| **Input Formatter** | Request Body | ต้องการรับไฟล์ **CSV**, **Protobuf**, หรือ **Image** |
| **Model Binder** | Parameter / Property | ต้องการ **Decrypt ID** หรือรวมข้อมูลจาก Query + Header |
| **TypeConverter** | String to Object | แปลง String รูปแบบพิเศษ (เช่น **12h-Format**) เป็น Object |
| **Value Provider** | Data Source | ต้องการดึงค่าจาก **Cookies** มาใส่ใน Parameter อัตโนมัติ |
