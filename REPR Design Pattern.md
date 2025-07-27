Request-Endpoint-Response

https://deviq.com/design-patterns/repr-design-pattern

รูปแบบการออกแบบ REPR (Request, Endpoint, Response) ซึ่งเป็นทางเลือกที่เรียบง่ายกว่าสำหรับการพัฒนา API เมื่อเทียบกับรูปแบบ MVC (Model, View, Controller) แบบดั้งเดิม REPR มุ่งเน้นไปที่การสร้างเอนด์พอยต์ API แต่ละรายการ โดยมีประเภทคำขอ (Request) และประเภทการตอบกลับ (Response) ที่ชัดเจน ทำให้ Controller ไม่บวม และช่วยให้โฟกัสไปที่ตรรกะเฉพาะจุดได้ดีขึ้น 

เน้นย้ำว่า REPR ไม่ใช่รูปแบบเฉพาะสำหรับ REST แต่สามารถใช้ได้ทั้งทรัพยากรแบบ RESTful และเอนด์พอยต์สไตล์ RPC


```c#
// https://github.com/ardalis/ApiEndpoints

public class MyEndpoint : EndpointBaseSync
 .WithRequest<string>
 .WithActionResult<IActionResult>
 {
     [HttpGet("my-endpoint")]
     public override ActionResult<IActionResult> Handle(string request)
     {
         // Your logic here

         return Ok();
     }
 }
```
