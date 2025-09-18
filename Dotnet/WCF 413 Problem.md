สรุปปัญหา **WCF error 413 (Request Entity Too Large):**

* **สาเหตุหลัก**

  1. ข้อมูลที่ client ส่ง > limit ของ WCF binding (`maxReceivedMessageSize`, `maxBufferSize`, `readerQuotas`)
  2. Host ที่เป็น IIS มี limit เอง (`maxAllowedContentLength` ใน `<requestFiltering>`)
  3. ใช้ **Buffered transfer** ซึ่งต้องโหลดทั้งหมดเข้า memory ก่อน ทำให้เจอ limit เร็ว

* **แนวทางแก้**

  * เพิ่มค่า `maxReceivedMessageSize`, `maxBufferSize`, `readerQuotas.*` ในทั้งฝั่ง service และ client
  * แก้ `maxAllowedContentLength` ใน IIS ถ้า host ผ่าน IIS
  * ถ้าไฟล์ใหญ่มาก (ร้อย MB ขึ้นไป) → เปลี่ยนเป็น `transferMode="Streamed"` และใช้ `Stream` แทน object ปกติ

รหัส 413 = ข้อความที่ client ส่งไป **ใหญ่เกินกว่าที่ service ยอมรับ**

### ต้นเหตุ

* ค่า `maxReceivedMessageSize` ของ service/binding ต่ำกว่าขนาดจริง
* ค่า `maxBufferSize`, `readerQuotas.maxArrayLength` เล็กเกินไป
* IIS เองมี limit ของ request body (`maxAllowedContentLength`)

---

### วิธีแก้

#### 1. Config ฝั่ง **Service** (`web.config` หรือ `app.config`)

```xml
<bindings>
  <basicHttpBinding>
    <binding name="LargeDataBinding"
             maxBufferSize="2147483647"
             maxReceivedMessageSize="2147483647"
             transferMode="Buffered">
      <readerQuotas maxDepth="32"
                    maxArrayLength="2147483647"
                    maxBytesPerRead="2147483647"
                    maxNameTableCharCount="2147483647"
                    maxStringContentLength="2147483647"/>
    </binding>
  </basicHttpBinding>
</bindings>

<services>
  <service name="YourNamespace.FileService">
    <endpoint address=""
              binding="basicHttpBinding"
              bindingConfiguration="LargeDataBinding"
              contract="YourNamespace.IFileService" />
  </service>
</services>
```

#### 2. Config ฝั่ง **Client** (`app.config`)

ต้องตรงกับฝั่ง service

```xml
<bindings>
  <basicHttpBinding>
    <binding name="LargeDataBinding"
             maxBufferSize="2147483647"
             maxReceivedMessageSize="2147483647">
      <readerQuotas maxDepth="32"
                    maxArrayLength="2147483647"
                    maxBytesPerRead="2147483647"
                    maxNameTableCharCount="2147483647"
                    maxStringContentLength="2147483647"/>
    </binding>
  </basicHttpBinding>
</bindings>
```

#### 3. IIS Limit (ถ้า host บน IIS) (แบบนี้ Work)

เพิ่มใน `web.config` (ระดับ `<system.webServer>`):

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- 500 MB -->
      <requestLimits maxAllowedContentLength="524288000" />
    </requestFiltering>
  </security>
</system.webServer>
```

ค่า `maxAllowedContentLength` วัดเป็น **byte**

---

### 4. ถ้าข้อมูลใหญ่มาก (เกินร้อย MB → GB)

ไม่ควรใช้ buffered mode → ควรเปลี่ยนเป็น **streamed transfer**

```xml
<binding name="LargeDataBinding"
         transferMode="Streamed"
         maxReceivedMessageSize="2147483647" />
```
