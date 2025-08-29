# Constructor (คอนสตรัคเตอร์) ใน JavaScript 

คือ "ฟังก์ชันพิเศษ" ที่ใช้เป็นแม่แบบ (template) สำหรับการสร้างอ็อบเจ็กต์ (object) ที่มีโครงสร้างและคุณสมบัติคล้ายกันกับ Class ในภาษา OOP

# วิธีการสร้าง Constructor
1. ประกาศฟังก์ชัน ขึ้นต้นด้วยตัวพิมพ์ใหญ่ จะรับหรือไม่รับ parameter ก็ได้, หากรับจะต้องแทนค่าด้วยคำสั่ง `this` (Encapsulation)
2. สร้าง Constructor ด้วยการเรียกใช้ฟังก์ชันดังกล่าว โดยระบุ `new` ด้านหน้าฟังก์ชันเหมือนภาษา OOP
3. จะได้อ็อบเจ็กต์ขึ้นมาหนึ่งตัว ({...})

# ตัวอย่างโค้ด
```js
function Person(name) {
  this.name = name;

  this.showInfo = function () {
    console.log(`Person: ${alice.name}`);
  };
}

var person = new Person('Alice');

person.showInfo();

console.log(person.name);
```

# หน้าของ Object
```js
Person {name: "Alice", showInfo: function()...}
  name: "Alice"
  showInfo: ƒ ()
  constructor: ƒ Person()
  [[Prototype]]: {}
```
