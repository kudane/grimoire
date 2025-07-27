Types as axioms

https://lexi-lambda.github.io/blog/2020/08/13/types-as-axioms-or-playing-god-with-static-types/

ไม่ต้องมองว่า type เป็น ข้อจำกัด (restrictive constraints) แบบที่หลายภาษาเสนอ ถ้าเปลี่ยนมุมมองว่า type คือ axioms (ข้อสมมติ) — คุณคือผู้กำหนดกฎ คุณคือพระเจ้าของ type-level universe แทนที่จะใช้ type checker เพื่อป้องกันค่าแปลกปลอม Type จะ กำหนดสิ่งที่เป็นไปได้ — logic ทั้งหมดย์ต้องสอดคล้องกับ axioms ที่คุณสร้างเอง  

ถ้าจะเอาแนวคิด “Types as axioms” มาใช้เราต้องจำลองว่าทุก object ที่ผ่าน type นี้ ต้องเป็นไปตามกฎที่เรากำหนด ตั้งแต่ตอนสร้าง

```c#
// Type เป็น "axiom" — ไม่มี AccountBalance ที่ติดลบได้
public sealed class AccountBalance
{
    public decimal Value { get; }

    private AccountBalance(decimal value)
    {
        Value = value;
    }

    // Factory method บังคับ invariant
    public static AccountBalance Create(decimal value)
    {
        if (value < 0)
            throw new ArgumentException("Balance cannot be negative.");
        return new AccountBalance(value);
    }

    public AccountBalance Add(decimal amount) => Create(Value + amount);
    public AccountBalance Subtract(decimal amount) => Create(Value - amount);
}

// การใช้งาน
var balance = AccountBalance.Create(100); // ✅ ถูกต้อง
var newBalance = balance.Subtract(30);    // ✅ ยังไม่ติดลบ
var bad = AccountBalance.Create(-50);     // ❌ ไม่คอมไพล์ผ่าน (exception)
```

ผลลัพธ์: โค้ดส่วนอื่น มั่นใจได้ 100% ว่า AccountBalance จะไม่ติดลบ เพราะ type บังคับกฎนี้ตั้งแต่จุดสร้าง — นี่คือการ “เล่นพระเจ้า” ของโลก type
