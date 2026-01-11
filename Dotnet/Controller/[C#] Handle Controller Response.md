```c#
builder.Services.AddProblemDetails(options =>
{
    options.CustomizeProblemDetails = context =>
    {
        var statusCode = context.ProblemDetails.Status;

        // จัดการ Status 400 (Validation Errors)
        if (statusCode == StatusCodes.Status400BadRequest)
        {
            context.ProblemDetails.Title = "ข้อมูลที่ส่งมาไม่ถูกต้อง";
            context.ProblemDetails.Detail = "กรุณาตรวจสอบความถูกต้องของฟิลด์ต่างๆ อีกครั้ง";
            context.ProblemDetails.Extensions["type"] = "validation_error";
        }

        // จัดการ Status 415 (Unsupported Media Type)
        if (statusCode == StatusCodes.Status415UnsupportedMediaType)
        {
            context.ProblemDetails.Title = "รูปแบบไฟล์ไม่รองรับ";
            context.ProblemDetails.Detail = "ระบบรับเฉพาะ JSON เท่านั้น (Content-Type: application/json)";
            context.ProblemDetails.Extensions["code"] = "INVALID_MEDIA_TYPE";
        }

        // จัดการ Status 500 (Internal Server Error)
        if (statusCode == StatusCodes.Status500InternalServerError)
        {
            context.ProblemDetails.Title = "เกิดข้อผิดพลาดภายในเซิร์ฟเวอร์";
            context.ProblemDetails.Detail = "ขออภัยในความไม่สะดวก ทีมงานกำลังเร่งตรวจสอบ";
            // ใน Environment Development อาจจะใส่ StackTrace เพิ่มได้
            context.ProblemDetails.Extensions["instance"] = context.HttpContext.Request.Path;
        }

        // เพิ่ม Trace ID ให้ทุก Response เพื่อใช้ในการ Debug
        context.ProblemDetails.Extensions["traceId"] = context.HttpContext.TraceIdentifier;
    };
})
```
