# Mental Model: 4 เสาหลักแปลง Business Logic สู่ Clean Architecture

---

### 1. The Core Execution Pipeline (Input $\rightarrow$ Process $\rightarrow$ Output)

แบ่งขั้นตอนการทำงานของทุก Use Case ออกเป็น 4 ลำดับตายตัว หลีกเลี่ยงการเขียนสคริปต์ยาวปนกันจากบนลงล่าง

* **Fetch & Guard:**
* ดึงข้อมูลที่จำเป็นทั้งหมดลง Memory ภายในรอบเดียว (หลีกเลี่ยง N+1 Query)
* ใช้ **Guard Clauses** ดักข้อมูลที่ไม่ถูกต้องหรือไม่เข้าเงื่อนไข แล้วสั่ง `return` / `continue` ทันทีเพื่อลด Depth ของโค้ด


* **Classify & Partition:**
* จับคู่ข้อมูล (Input vs Existing Data)
* จัดกลุ่มตาม "เจตนาทางธุรกิจ" (Intent) โดย**ยังไม่เปลี่ยนแปลงสถานะข้อมูล (No Side Effects)**


* **Execute Domain Behaviors:**
* รัน Business Rules กับข้อมูลแต่ละกลุ่มผ่าน Domain Method หรือ Sub-function เฉพาะทาง


* **Persist & Return:**
* รวมศูนย์การเปลี่ยนแปลงสถานะ (State Changes) แล้วบันทึกลง Database ใน Batch เดียว (Atomic/Transaction) พร้อมแปลง Model ส่งออก



---

### 2. Separation of Concerns: Decider vs. Worker

แยก "การตัดสินใจ" ออกจาก "การลงมือทำ" อย่างเด็ดขาด

* **Decider (Pure Logic):**
* ตรวจสอบ Rule และกำหนดว่าจะเกิด Action อะไรขึ้น
* ต้องเป็น **Pure Function** (ไม่มี I/O, ไม่มี Side Effects, ไม่เรียก Database)
* ข้อดี: เขียน Unit Test ตรวจสอบ Logic ได้ครอบคลุมและรวดเร็ว


* **Worker (Side Effects & I/O):**
* รับคำสั่งที่ Decider ตัดสินใจแล้วไปดำเนินการจริง (Query, Mutate, Save, Send Event)
* เมื่อเงื่อนไขทางธุรกิจเปลี่ยน จะแก้ไขเฉพาะที่ Decider โดยไม่กระทบโครงสร้างการต่อ I/O



---

### 3. Encapsulate Behavior (Tell, Don't Ask)

เลิกใช้ Entity เป็นเพียงถังพักข้อมูล (Anemic Domain Model) แล้วให้ Service ดึงค่ามา `get` เช็คเงื่อนไข แล้ว `set` กลับไปเอง

* **สั่งให้ Entity จัดการตัวเอง:** นำ Logic การเปลี่ยน State ย้ายเข้าไปเป็น Behavior (Method) ภายใน Entity
* **ตัวอย่างการเปลี่ยน Mindset:**
* *เดิม (Ask):* `edit.NextTranId = newTxnId; inactiveList.Add(edit);`
* *ใหม่ (Tell):* `edit.CloseTransaction(newTxnId);`


* **ประโยชน์:** ป้องกัน Logic ทางธุรกิจรั่วไหล (Logic Leakage) ออกมาปนใน Presentation หรือ Application Layer

---

### 4. Rule-Based Classification (Strategy / Specification)

ป้องกันปัญหา `if-else` หรือ `switch-case` บวมโตจนควบคุมไม่ได้เมื่อ Requirement ซับซ้อนขึ้น

* **แยก Rule เป็นชิ้นส่วนอิสระ:** แปลงแต่ละเงื่อนไขทางธุรกิจให้อยู่ใน Interface หรือ Class เฉพาะ เช่น:
* `bool IsSatisfiedBy(...)` $\rightarrow$ ตรวจสอบว่าเข้าเงื่อนไขนี้หรือไม่
* `void Apply(...)` $\rightarrow$ ดำเนินการตาม Rule นั้น


* **Open/Closed Principle:** เมื่อมีเงื่อนไขธุรกิจใหม่ ให้สร้าง Rule Class ใหม่ขึ้นมาต่อเติม แทนการเข้าไปแทรกบล็อก `if-else` เพิ่มในลูปเดิม

---

### Quick Self-Check Checklist ก่อน Commit Code

* [ ] **Indent Level:** โค้ดไม่มีการ Nest ซ้อนเกิน 2 ชั้น (ไม่มี `for` $\rightarrow$ `if` $\rightarrow$ `if`)
* [ ] **I/O Boundaries:** การยิง Database (Read/Write) อยู่แค่หัวและท้ายของ Pipeline ตรงกลางเป็น In-Memory Transformation ล้วน
* [ ] **Single Mutation Point:** ข้อมูลถูกเปลี่ยนค่าในจุดที่กำหนดชัดเจน ไม่กระจายตามเงื่อนไขย่อย
* [ ] **Explicit Intent:** ชื่อฟังก์ชันหรือตัวแปรบอกเจตนาทางธุรกิจ (Business Intent) มากกว่าบอกกลไกทางเทคนิค (Technical Implementation)
