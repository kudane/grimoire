Thread Safe คือ คุณสมบัติของโปรแกรมหรือโค้ดที่ทำงานได้อย่างถูกต้องและคาดการณ์ได้ แม้ว่าจะถูกเรียกใช้งานโดยเธรดหลายๆ เธรดพร้อมๆ กัน หรือกล่าวอีกนัยหนึ่งคือ ป้องกันไม่ให้เกิดปัญหาต่างๆ เช่น สภาวะการแข่งขัน (race condition) หรือ ภาวะเดดล็อก (deadlock) เมื่อมีการเข้าถึงข้อมูลหรือทรัพยากรร่วมกัน

ใน C# มีโอกาสเกิด Thread Un-safe ไม่ยากหรอก ควรทำความเข้าใจคำสั่งหรือเครื่องมือบางตัว อย่างเช่น DbContext ที่การใช้งานจำเป็นต้อง Per-Thread เท่านั้น(default)

ปัญหาของ Thread Un-safe มักพบเจอจาก
1. การเข้าถึงทรัพยากรแบบ race condition ก่อให้เกิด bug และ exception
2. การใช้งาน multiple thread แบบผิดๆ ก่อให้เกิด runtime exception เช่น ConfigurationAwait(false)
3. การใช้งานแบบข้าม boundary context ของการทำงานของ framework

ใน Asp.Net Web API การทำงานของ Request Boundary Context ก็คือ 1 endpoint ต่อ 1 request boundary context

ภายในของ request boundary context จะประกอบไปด้วยหลายๆ instances ที่ถูกสร้างมาเพื่อการทำ API บางอย่างต้องเขียนคำสั่งให้ทำงานแบบ Thread Safe เพราะเมื่อใช้ multiple thread แบบผิดๆ จะเป็นการออกจาก request boundary context แล้วเกิด runtime error(debug ยากมาก)

service ของ Asp.Net Web API มีด้วยกัน 3 แบบ คือ
1.Singleton คือ สร้างครั้งเดียวตอนรันแอพ ระวังการทำงานแบบ race condition และการไปทรัพยากรใน request boundary context เช่น Http Context
2.Scoped คือ สร้างตาม request boundary context ใช้ทรัพยากรใน rbc ได้เต็มที่ ระวังการทำงาน multiple thread
3.Transient คือ สร้างใหม่ทุกครั้ง ระวังการนำไปใช้ในงานหนัก เพราะกินหน่วยความจำมหาศาล




