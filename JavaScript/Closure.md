# Closure ในภาษา JavaScript 🧐

Closure (โคลเชอร์) คือหนึ่งในคอนเซ็ปต์ที่ทรงพลังและสำคัญที่สุดในภาษา JavaScript มันคือความสามารถของฟังก์ชันที่สามารถ **"จดจำ"** และ **"เข้าถึง"** ตัวแปรจากสภาพแวดล้อม (Scope) ที่มันถูกสร้างขึ้นได้ ถึงแม้ว่าสภาพแวดล้อมนั้นจะทำงานเสร็จสิ้นไปแล้วก็ตาม

ถ้าจะให้อธิบายง่ายๆ Closure ก็เปรียบเสมือนฟังก์ชันที่พก "กระเป๋าเป้" ติดตัวไปด้วย ในกระเป๋าเป้นั้นบรรจุตัวแปรทั้งหมดที่อยู่รอบๆ ตัวมันตอนที่มันถูกสร้างขึ้นมา

-----

## หลักการทำงานและ Lexical Scope

หัวใจสำคัญที่ทำให้ Closure ทำงานได้คือ **Lexical Scope** ซึ่งเป็นกฎของ JavaScript ที่ว่า "ฟังก์ชันจะมองเห็นตัวแปรไหนได้บ้างนั้น ขึ้นอยู่กับ **ตำแหน่งที่ฟังก์ชันถูกเขียนหรือประกาศ** ในโค้ด ไม่ใช่ตำแหน่งที่มันถูกเรียกใช้"

ลำดับการเข้าถึงตัวแปรจะเป็นดังนี้:

1.  **Local Scope:** ค้นหาใน Scope ของตัวเองก่อน
2.  **Outer Scope:** หากไม่เจอ จะค้นหาใน Scope ของฟังก์ชันที่ห่อหุ้มมันอยู่ (ไล่ออกไปทีละชั้น)
3.  **Global Scope:** หากยังไม่เจอ จะค้นหาใน Scope ที่ใหญ่ที่สุดคือ Global

> 👉 **สรุปง่ายๆ:** "JavaScript สนใจว่าคุณ **เขียน** ฟังก์ชันไว้ที่ไหน ไม่ได้สนใจว่าคุณ **เรียก** มันจากที่ไหน"

-----

## โครงสร้างและตัวอย่างโค้ด

โครงสร้างพื้นฐานของ Closure คือการที่ฟังก์ชันหนึ่ง (Inner Function) ถูกประกาศและ return ออกมาจากอีกฟังก์ชันหนึ่ง (Outer Function)

### ตัวอย่างที่ 1: การสร้าง Counter

ลองดูตัวอย่างคลาสสิกที่แสดงให้เห็นความสามารถของ Closure ในการจดจำค่า:

```javascript
function makeCounter() {
  let count = 0; // ตัวแปรนี้เปรียบเสมือน state ที่ถูกเก็บไว้ใน "กระเป๋าเป้"

  // ฟังก์ชันที่ return ออกไปนี้คือ Closure
  return function() {
    count++; // Closure ยังคงเข้าถึงตัวแปร count ได้
    return count;
  };
}

const counterA = makeCounter(); // สร้าง counter ตัวที่หนึ่ง
const counterB = makeCounter(); // สร้าง counter ตัวที่สอง

// เรียกใช้ counterA
console.log(counterA()); // ผลลัพธ์: 1
console.log(counterA()); // ผลลัพธ์: 2

// เรียกใช้ counterB จะเห็นว่ามี state เป็นของตัวเอง ไม่ปนกับ counterA
console.log(counterB()); // ผลลัพธ์: 1
console.log(counterB()); // ผลลัพธ์: 2
```

**คำอธิบาย:**

  * ทุกครั้งที่เรียก `makeCounter()` จะมีการสร้าง `scope` ใหม่ พร้อมกับตัวแปร `count` ที่มีค่าเริ่มต้นเป็น `0`
  * ฟังก์ชันที่ถูก `return` ออกมา (ซึ่งเราเก็บไว้ในตัวแปร `counterA` และ `counterB`) คือ Closure
  * `counterA` และ `counterB` แต่ละตัวมี "กระเป๋าเป้" หรือสภาพแวดล้อมเป็นของตัวเอง ทำให้ `count` ของแต่ละตัวแยกจากกันโดยสิ้นเชิง
  * แม้ว่า `makeCounter()` จะทำงานจบไปแล้ว แต่ตัวแปร `count` ไม่ได้หายไปไหน เพราะ Closure ยังคงอ้างอิงถึงมันอยู่

-----

## ประโยชน์ของ Closure

Closure ไม่ได้เป็นเพียงคอนเซ็ปต์ทางทฤษฎี แต่ยังมีประโยชน์ในการใช้งานจริงอย่างมาก

### **1. การสร้างตัวแปรแบบ Private (Private Variables) และ Data Encapsulation**

เราสามารถใช้ Closure เพื่อสร้างตัวแปรที่ไม่สามารถเข้าถึงได้จากภายนอกโดยตรง แต่จะเข้าถึงได้ผ่านฟังก์ชันที่เรากำหนดไว้เท่านั้น ซึ่งเป็นหลักการพื้นฐานของ **Encapsulation** หรือการห่อหุ้มข้อมูล

```javascript
function createPerson(name) {
  let _name = name; // _name เป็น private variable

  return {
    getName: function() {
      return _name; // เข้าถึงได้ผ่าน method นี้เท่านั้น
    },
    setName: function(newName) {
      _name = newName; // แก้ไขค่าได้ผ่าน method นี้เท่านั้น
    }
  };
}

const person = createPerson('John');
console.log(person.getName()); // ผลลัพธ์: "John"
// console.log(person._name); // ไม่สามารถทำได้ จะได้ผลเป็น undefined
```

### **2. Factory Functions**

ใช้ในการสร้างฟังก์ชันที่มีการกำหนดค่าบางอย่างไว้ล่วงหน้า (Pre-configured)

```javascript
function makeGreeter(greeting) {
  return function(name) {
    console.log(`${greeting}, ${name}!`);
  };
}

const sayHello = makeGreeter('Hello');
const sayHi = makeGreeter('Hi');

sayHello('Alice'); // ผลลัพธ์: "Hello, Alice!"
sayHi('Bob');    // ผลลัพธ์: "Hi, Bob!"
```

### **3. การจัดการ Callback และ Event Handlers**

Closure มีประโยชน์อย่างมากในการเขียนโค้ดที่ทำงานแบบ Asynchronous เช่น การจัดการ Event ใน Web Development เพื่อให้แน่ใจว่า Callback Function สามารถเข้าถึงตัวแปรที่ถูกต้อง ณ เวลาที่มันถูกเรียกใช้

```javascript
// ตัวอย่างการใช้ใน for loop (ที่มักเป็นปัญหา)
for (var i = 1; i <= 3; i++) {
  // ใช้ IIFE (Immediately Invoked Function Expression) เพื่อสร้าง Scope ใหม่ในแต่ละรอบ
  (function(num) {
    setTimeout(function() {
      console.log(num); // num คือค่าที่ถูก "จำ" ไว้ในแต่ละรอบของ loop
    }, num * 1000);
  })(i);
}
// ผลลัพธ์:
// 1 (หลังจาก 1 วินาที)
// 2 (หลังจาก 2 วินาที)
// 3 (หลังจาก 3 วินาที)
```

**หมายเหตุ:** ใน JavaScript สมัยใหม่ (ES6+) เราสามารถใช้ `let` แทน `var` ใน `for` loop เพื่อแก้ปัญหานี้ได้ง่ายกว่า เพราะ `let` จะสร้าง Scope ใหม่ในแต่ละรอบของ Loop โดยอัตโนมัติ
