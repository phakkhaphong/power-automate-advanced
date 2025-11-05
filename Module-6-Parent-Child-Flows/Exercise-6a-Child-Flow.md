# Exercise 6a: Child Flow - Process Donor Type

## 🎯 วัตถุประสงค์

ใน Exercise นี้ คุณจะได้เรียนรู้การ:
- สร้าง **Child Flow** ที่สามารถนำกลับมาใช้ซ้ำได้
- ใช้ **HTTP Request Trigger** เพื่อรับ Input Parameters จาก Parent Flow
- ใช้ **Switch Condition** เพื่อจัดการหลาย Case (กรณี) ตามประเภทผู้บริจาค
- ส่ง Email ที่แตกต่างกันตามประเภทผู้บริจาค
- สร้าง Calendar Event สำหรับผู้บริจาคประเภทพิเศษ
- ใช้ **Response Action** เพื่อส่งผลลัพธ์กลับไปยัง Parent Flow

> 💡 **หมายเหตุ**: Exercise นี้เป็น **Child Flow** ที่จะถูกเรียกใช้จาก Parent Flow:
> - Child Flow รับ Input Parameters: DonorType, Name, Email, Amount
> - Switch Condition ตรวจสอบ DonorType และทำงานตาม Case
> - Case 1: "First time donor" → ส่ง Email แจ้งเตือน
> - Case 2: "Occasional donor" → ส่ง Email ขอบคุณ
> - Default: "All-the-time donor" → สร้าง Calendar Event
> - ส่ง Response กลับไปยัง Parent Flow

---

## 📋 สิ่งที่ต้องเตรียม

- Power Automate Account (สามารถใช้ Free Account ได้)
- ความรู้พื้นฐานเรื่อง Flow Creation และการใช้งาน Switch Condition
- Email Account ที่เชื่อมต่อกับ Power Automate
- Calendar Account (สำหรับสร้าง Event)
- ความรู้พื้นฐานเรื่อง HTTP Request/Response

---

## 🚀 ขั้นตอนการปฏิบัติ

### ขั้นตอนที่ 1: สร้าง Child Flow ใหม่

1. เข้าสู่ **Power Automate** (https://make.powerautomate.com/)
2. คลิก **Create** → เลือก **Instant cloud flow**
3. ตั้งชื่อ Flow: `Exercise 6a - Child Flow Process Donor Type`
4. คลิก **Create**

> 💡 **หมายเหตุ**: Child Flow ใช้ **Instant cloud flow** หรือ **Automated cloud flow** ก็ได้ แต่ต้องมี HTTP Request Trigger

### ขั้นตอนที่ 2: เพิ่ม HTTP Request Trigger

1. ใน Flow Designer ค้นหาและเลือก Trigger: **When an HTTP request is received**
2. Trigger นี้จะถูกเพิ่มอัตโนมัติ

> 💡 **คำอธิบาย**: HTTP Request Trigger จะทำให้ Child Flow รับ Request จาก Parent Flow หรือ External Systems

### ขั้นตอนที่ 3: กำหนด Request Body JSON Schema

1. คลิกที่ **When an HTTP request is received** Trigger
2. คลิก **Use sample payload to generate schema**
3. วาง JSON Schema ต่อไปนี้:
   ```json
   {
     "donorType": "First time donor",
     "name": "สมชาย ใจดี",
     "email": "somchai@example.com",
     "amount": 5000
   }
   ```
4. คลิก **Done**

> 💡 **คำอธิบาย**: JSON Schema จะกำหนด Input Parameters ที่ Child Flow รับ:
> - `donorType`: ประเภทผู้บริจาค (String)
> - `name`: ชื่อผู้บริจาค (String)
> - `email`: Email ผู้บริจาค (String)
> - `amount`: จำนวนเงินบริจาค (Number)

**Input Parameters ที่ Child Flow รับ**:
- `donorType` → ประเภทผู้บริจาค ("First time donor", "Occasional donor", "All-the-time donor")
- `name` → ชื่อผู้บริจาค
- `email` → Email ผู้บริจาค
- `amount` → จำนวนเงินบริจาค (Number)

### ขั้นตอนที่ 4: เพิ่ม Initialize Variable สำหรับ Response

1. เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **Initialize variable**
3. ตั้งค่า:
   - **Name**: `response`
   - **Type**: `Object`
   - **Value**: คลิก **Expression** (fx) และพิมพ์:
     ```json
     {
       "success": true,
       "message": "Donor processed successfully",
       "donorType": "",
       "action": ""
     }
     ```

> 💡 **คำอธิบาย**: Variable นี้จะเก็บ Response ที่จะส่งกลับไปยัง Parent Flow

### ขั้นตอนที่ 5: เพิ่ม Switch Condition

1. เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **Switch** (หรือ **Control** → **Switch**)
3. ตั้งค่า:
   - **On**: เลือก `donorType` จาก Dynamic Content (จาก HTTP Request Body)

> 💡 **คำอธิบาย**: Switch จะตรวจสอบค่าของ `donorType` และทำงานตาม Case ที่ตรงกัน
> - **Case 1**: "First time donor" → ส่ง Email แจ้งเตือน
> - **Case 2**: "Occasional donor" → ส่ง Email ขอบคุณ
> - **Default**: "All-the-time donor" → สร้าง Calendar Event

**Switch Structure**:
```
Switch: donorType
  ├─ Case 1: "First time donor" → Send Email (V2)
  ├─ Case 2: "Occasional donor" → Send Email (V2)
  └─ Default: "All-the-time donor" → Create Event (V4)
```

### ขั้นตอนที่ 6: เพิ่ม Case 1 - "First time donor"

1. **ภายใน Case 1** (คลิกที่ Case 1 หรือ **Add case** ถ้ายังไม่มี)
2. ตั้งค่า Case:
   - **Equals**: พิมพ์ `First time donor` (ตรงกับค่าจาก Input)

3. **ภายใน Case 1** คลิก **Add an action**
4. ค้นหาและเลือก **Send an email (V2)**
5. ตั้งค่า:
   - **To**: ใส่ Email address ของคุณ (เช่น `your-email@example.com`)
   - **Subject**: พิมพ์ `We have a first time donor`
   - **Body**: ใช้ Dynamic Content เพื่อสร้างข้อความ:
     ```
     Let's make sure to reach out of them.
     
     Name: {name}
     Email: {email}
     Amount: {amount}
     ```

6. **หลังจาก Send Email** เพิ่ม Action: **Set variable**
   - **Name**: `response`
   - **Value**: คลิก **Expression** (fx) และพิมพ์:
     ```json
     {
       "success": true,
       "message": "First time donor email sent",
       "donorType": "@{body('When_an_HTTP_request_is_received')?['donorType']}",
       "action": "Email sent to admin"
     }
     ```

> 💡 **คำอธิบาย**: Action นี้จะส่ง Email เมื่อผู้บริจาคเป็น "First time donor"
> - Subject: `We have a first time donor`
> - Body: รวมข้อมูลชื่อ, Email, และจำนวนเงินบริจาค
> - Update Response Variable: บันทึกผลลัพธ์

**ตัวอย่าง Email ที่ส่งออกไป**:
- **To**: `your-email@example.com`
- **Subject**: `We have a first time donor`
- **Body**: 
  ```
  Let's make sure to reach out of them.
  
  Name: สมชาย ใจดี
  Email: somchai@example.com
  Amount: 5000
  ```

### ขั้นตอนที่ 7: เพิ่ม Case 2 - "Occasional donor"

1. **ภายใน Switch** คลิก **Add case** เพื่อเพิ่ม Case 2
2. ตั้งค่า Case:
   - **Equals**: พิมพ์ `Occasional donor` (ตรงกับค่าจาก Input)

3. **ภายใน Case 2** คลิก **Add an action**
4. ค้นหาและเลือก **Send an email (V2)**
5. ตั้งค่า:
   - **To**: เลือก `email` จาก Dynamic Content (จาก HTTP Request Body)
   - **Subject**: พิมพ์ `Thank you for donating`
   - **Body**: พิมพ์ `Thank you so much more donating - you are clearly an occasional donor`

6. **หลังจาก Send Email** เพิ่ม Action: **Set variable**
   - **Name**: `response`
   - **Value**: คลิก **Expression** (fx) และพิมพ์:
     ```json
     {
       "success": true,
       "message": "Thank you email sent to donor",
       "donorType": "@{body('When_an_HTTP_request_is_received')?['donorType']}",
       "action": "Email sent to donor"
     }
     ```

> 💡 **คำอธิบาย**: Action นี้จะส่ง Email ขอบคุณไปยังผู้บริจาคเมื่อเป็น "Occasional donor"
> - To: Email จาก Input (ส่งไปยังผู้บริจาคโดยตรง)
> - Subject: `Thank you for donating`
> - Body: ข้อความขอบคุณ
> - Update Response Variable: บันทึกผลลัพธ์

**ตัวอย่าง Email ที่ส่งออกไป**:
- **To**: `donor@example.com` (Email จาก Input)
- **Subject**: `Thank you for donating`
- **Body**: `Thank you so much more donating - you are clearly an occasional donor`

### ขั้นตอนที่ 8: เพิ่ม Default Case - "All-the-time donor"

1. **ภายใน Switch** คลิกที่ **Default** case (หรือสร้าง Default ถ้ายังไม่มี)
2. **ภายใน Default** คลิก **Add an action**
3. ค้นหาและเลือก **Create event (V4)**
4. ตั้งค่า:
   - **Calendar id**: เลือก Calendar ของคุณ (เช่น `ปฏิทิน` หรือ `Calendar`)
   - **Subject**: พิมพ์ `We have all-time-donor! Let Discuss how to bring him /her in`
   - **Start time**: คลิก **Expression** (fx) และพิมพ์:
     ```
     addDays(utcNow(), 1)
     ```
   - **End time**: คลิก **Expression** (fx) และพิมพ์:
     ```
     addHours(addDays(utcNow(), 1), 1)
     ```
   - **Time zone**: เลือก `(UTC+07:00) Bangkok, Hanoi, Jakarta` (หรือ Time zone ที่คุณต้องการ)
   - **Required attendees**: เพิ่ม Email address ของเพื่อนร่วมงาน (เช่น `colleague@example.com`)

5. **หลังจาก Create Event** เพิ่ม Action: **Set variable**
   - **Name**: `response`
   - **Value**: คลิก **Expression** (fx) และพิมพ์:
     ```json
     {
       "success": true,
       "message": "Calendar event created for all-time donor",
       "donorType": "@{body('When_an_HTTP_request_is_received')?['donorType']}",
       "action": "Calendar event created"
     }
     ```

> 💡 **คำอธิบาย**: Action นี้จะสร้าง Calendar Event เมื่อผู้บริจาคเป็น "All-the-time donor"
> - Subject: `We have all-time-donor! Let Discuss how to bring him /her in`
> - Start/End time: พรุ่งนี้
> - Required attendees: เชิญเพื่อนร่วมงานเข้าร่วมประชุม
> - Update Response Variable: บันทึกผลลัพธ์

**ตัวอย่าง Calendar Event**:
- **Subject**: `We have all-time-donor! Let Discuss how to bring him /her in`
- **Start time**: พรุ่งนี้ เวลา 00:00
- **End time**: พรุ่งนี้ เวลา 01:00
- **Time zone**: (UTC+07:00) Bangkok, Hanoi, Jakarta
- **Required attendees**: `colleague@example.com`

### ขั้นตอนที่ 9: เพิ่ม Response Action

1. **หลังจาก Switch** (นอก Switch) เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **Response**
3. ตั้งค่า:
   - **Status Code**: `200`
   - **Headers**: ไม่ต้องเพิ่ม (หรือเพิ่ม `Content-Type: application/json`)
   - **Body**: เลือก `response` จาก Dynamic Content (Variable)

> 💡 **คำอธิบาย**: Response Action จะส่งผลลัพธ์กลับไปยัง Parent Flow
> - Status Code: 200 (Success)
> - Body: Response Variable ที่มีข้อมูล success, message, donorType, action

**Response Structure**:
```json
{
  "success": true,
  "message": "Donor processed successfully",
  "donorType": "First time donor",
  "action": "Email sent to admin"
}
```

### ขั้นตอนที่ 10: Save และ Copy Flow URL

1. คลิก **Save**
2. หลังจาก Save สำเร็จ ให้ Copy **HTTP POST URL** จาก Trigger
   - คลิกที่ **When an HTTP request is received**
   - Copy URL ที่แสดงใน Trigger (เช่น `https://prod-xxx.azure-apim.net/triggers/manual/...`)

> 💡 **หมายเหตุ**: URL นี้จะใช้ใน Parent Flow เพื่อเรียกใช้ Child Flow

---

## 📊 ผลลัพธ์ที่คาดหวัง

### Child Flow Structure

```
When an HTTP request is received
  ↓
Initialize variable: response
  ↓
Switch: donorType
  ├─ Case 1: "First time donor"
  │   ├─ Send Email (V2) - "We have a first time donor"
  │   └─ Set variable: response
  ├─ Case 2: "Occasional donor"
  │   ├─ Send Email (V2) - "Thank you for donating"
  │   └─ Set variable: response
  └─ Default: "All-the-time donor"
      ├─ Create Event (V4) - "We have all-time-donor! Let Discuss how to bring him /her in"
      └─ Set variable: response
  ↓
Response: Return response variable
```

### Input Parameters

Child Flow รับ Input Parameters ผ่าน HTTP Request Body:
```json
{
  "donorType": "First time donor",
  "name": "สมชาย ใจดี",
  "email": "somchai@example.com",
  "amount": 5000
}
```

### Output Response

Child Flow ส่ง Response กลับไปยัง Parent Flow:
```json
{
  "success": true,
  "message": "Donor processed successfully",
  "donorType": "First time donor",
  "action": "Email sent to admin"
}
```

---

## 🎓 สิ่งที่ได้เรียนรู้

✅ **Child Flow Concept**: สร้าง Flow ที่สามารถนำกลับมาใช้ซ้ำได้  
✅ **HTTP Request Trigger**: ใช้ "When an HTTP request is received" เพื่อรับ Input Parameters  
✅ **Request Body JSON Schema**: กำหนด Schema ของ Input Parameters  
✅ **Switch Condition**: ใช้ Switch เพื่อจัดการหลาย Case จากค่าเดียว  
✅ **Dynamic Content**: ใช้ Dynamic Content จาก HTTP Request Body ใน Actions  
✅ **Response Action**: ส่งผลลัพธ์กลับไปยัง Parent Flow  
✅ **Variable Management**: ใช้ Variable เพื่อเก็บ Response  
✅ **Email Integration**: ส่ง Email ผ่าน Office 365 Outlook  
✅ **Calendar Integration**: สร้าง Calendar Event อัตโนมัติ  
✅ **Date/Time Functions**: ใช้ `addDays()`, `addHours()`, `utcNow()` เพื่อจัดการวันที่/เวลา  

---

## 💡 Tips & Tricks

1. **HTTP Request Trigger**:
   - Child Flow ต้องใช้ HTTP Request Trigger
   - กำหนด JSON Schema เพื่อรับ Input Parameters
   - Copy HTTP POST URL เพื่อใช้ใน Parent Flow

2. **Response Action**:
   - ใช้ Response Action เพื่อส่งผลลัพธ์กลับไปยัง Parent Flow
   - Status Code: 200 (Success), 400 (Bad Request), 500 (Internal Server Error)
   - Body: JSON Object ที่มีข้อมูลผลลัพธ์

3. **Variable Management**:
   - ใช้ Variable เพื่อเก็บ Response ที่จะส่งกลับ
   - Update Variable ในแต่ละ Case
   - ใช้ Variable ใน Response Action

4. **Error Handling**:
   - ควรจัดการ Error ใน Child Flow
   - ส่ง Error Response กลับไปยัง Parent Flow เมื่อเกิด Error
   - ใช้ Try-Catch Pattern (ถ้าเป็นไปได้)

5. **Testing Child Flow**:
   - ใช้ **Test** mode ใน Power Automate เพื่อทดสอบ Child Flow
   - ใช้ **Postman** หรือ **HTTP Client** เพื่อทดสอบ HTTP Request
   - ตรวจสอบ Response ที่ส่งกลับ

---

## 🔄 แบบฝึกหัดเพิ่มเติม

1. **เพิ่ม Error Handling**:
   - เพิ่ม Try-Catch Pattern สำหรับจัดการ Error
   - ส่ง Error Response เมื่อเกิด Error
   - ใช้ Status Code 400 หรือ 500 สำหรับ Error

2. **เพิ่ม Input Validation**:
   - ตรวจสอบว่า Input Parameters ครบถ้วน
   - ตรวจสอบรูปแบบ Email
   - ตรวจสอบว่าจำนวนเงินเป็น Positive Number

3. **เพิ่ม Logging**:
   - บันทึก Log ทุกครั้งที่มีการเรียกใช้ Child Flow
   - ใช้ SharePoint List หรือ Storage Account เพื่อเก็บ Log

4. **เพิ่ม Response Fields**:
   - เพิ่ม Field ใน Response เช่น `timestamp`, `processingTime`, `donorId`
   - ใช้ข้อมูลเหล่านี้ใน Parent Flow

5. **สร้าง Multiple Child Flows**:
   - สร้าง Child Flow สำหรับการ Validate Email
   - สร้าง Child Flow สำหรับการ Calculate Discount
   - ใช้ Child Flow หลายตัวใน Parent Flow

---

## ❓ คำถามที่พบบ่อย (FAQ)

**Q: Child Flow คืออะไร?**  
A: Child Flow คือ Flow ที่ถูกสร้างขึ้นเพื่อให้ Flow อื่นเรียกใช้ เหมือนฟังก์ชันในโปรแกรม

**Q: ทำไมต้องใช้ HTTP Request Trigger?**  
A: เพราะ Child Flow ต้องรับ Input Parameters จาก Parent Flow หรือ External Systems

**Q: วิธีกำหนด JSON Schema?**  
A: ใช้ "Use sample payload to generate schema" และวาง JSON Sample เพื่อสร้าง Schema อัตโนมัติ

**Q: Response Action คืออะไร?**  
A: Response Action คือ Action ที่ส่งผลลัพธ์กลับไปยัง Parent Flow หรือ HTTP Client

**Q: วิธีทดสอบ Child Flow?**  
A: ใช้ Test mode ใน Power Automate หรือใช้ Postman/HTTP Client เพื่อทดสอบ HTTP Request

**Q: สามารถใช้ Child Flow ใน Flow อื่นได้ไหม?**  
A: ได้, Child Flow สามารถถูกเรียกใช้จาก Parent Flow หรือ External Systems ผ่าน HTTP Request

**Q: วิธี Copy HTTP POST URL?**  
A: คลิกที่ HTTP Request Trigger แล้ว Copy URL ที่แสดงใน Trigger

**Q: Response Variable ต้องเป็น Object หรือไม่?**  
A: ไม่จำเป็น แต่ควรเป็น Object เพื่อให้ง่ายต่อการจัดการและส่งกลับ

---

## 📝 สรุป

ใน Exercise นี้ คุณได้เรียนรู้:
- การสร้าง Child Flow ที่สามารถนำกลับมาใช้ซ้ำได้
- การใช้ HTTP Request Trigger เพื่อรับ Input Parameters
- การกำหนด Request Body JSON Schema
- การใช้ Switch Condition เพื่อจัดการหลาย Case
- การส่ง Email และสร้าง Calendar Event ตาม Case
- การใช้ Response Action เพื่อส่งผลลัพธ์กลับไปยัง Parent Flow
- การใช้ Variable เพื่อเก็บ Response

**Child Flow Structure**:
```
When an HTTP request is received
  ↓
Initialize variable: response
  ↓
Switch: donorType
  ├─ Case 1: "First time donor" → Send Email + Set variable
  ├─ Case 2: "Occasional donor" → Send Email + Set variable
  └─ Default: "All-the-time donor" → Create Event + Set variable
  ↓
Response: Return response variable
```

**ข้อดีของการใช้ Child Flow**:
- ✅ Code Reusability (นำกลับมาใช้ซ้ำได้)
- ✅ Maintenance Efficiency (ง่ายต่อการบำรุงรักษา)
- ✅ Consistency (ความสอดคล้อง)
- ✅ Modularity (แยกเป็นโมดูล)

**เมื่อไหร่ควรใช้ Child Flow**:
- ✅ Logic ที่ใช้ซ้ำในหลาย Flow
- ✅ Operations ที่ซับซ้อนและต้องการแยกออกมา
- ✅ กระบวนการมาตรฐานที่ใช้ทั่วทั้งองค์กร
- ✅ ฟังก์ชัน Utility ที่ใช้ร่วมกัน

พร้อมสำหรับ Exercise 6b (Parent Flow) แล้ว! 🎉

