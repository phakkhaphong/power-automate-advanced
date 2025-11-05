# Exercise 6b: Parent Flow - Forms → Call Child Flow

## 🎯 วัตถุประสงค์

ใน Exercise นี้ คุณจะได้เรียนรู้การ:
- สร้าง **Parent Flow** ที่เรียกใช้ Child Flow
- ใช้ **Microsoft Forms Trigger** ("When a new response is submitted")
- ใช้ **Get response details** เพื่อดึงข้อมูลจากฟอร์ม
- ส่งข้อความแจ้งเตือนไปยัง Teams
- ใช้ **HTTP Request Action** เพื่อเรียกใช้ Child Flow
- ส่ง Input Parameters ไปยัง Child Flow
- รับ Response จาก Child Flow และจัดการผลลัพธ์

> 💡 **หมายเหตุ**: Exercise นี้เป็น **Parent Flow** ที่เรียกใช้ Child Flow (Exercise 6a):
> - Parent Flow รับ Trigger จาก Forms
> - ดึงข้อมูลจาก Forms (Name, Email, Amount, DonorType)
> - ส่งข้อความแจ้งเตือนไปยัง Teams
> - เรียกใช้ Child Flow โดยส่ง Input Parameters
> - รับ Response จาก Child Flow และจัดการผลลัพธ์

---

## 📋 สิ่งที่ต้องเตรียม

- Power Automate Account (สามารถใช้ Free Account ได้)
- Microsoft Forms Account (สำหรับสร้างฟอร์ม "Charity Form")
  - 💡 **หมายเหตุ**: ใช้ฟอร์ม "Charity Form" เดียวกับ Exercise 3c
- Child Flow ที่สร้างเสร็จแล้ว (Exercise 6a)
  - 💡 **หมายเหตุ**: ต้องสร้าง Child Flow ก่อน (Exercise 6a)
  - HTTP POST URL ของ Child Flow
- ความรู้พื้นฐานเรื่อง Flow Creation และการใช้งาน HTTP Request
- Email Account ที่เชื่อมต่อกับ Power Automate
- Teams Account (สำหรับส่งข้อความ)

---

## 🚀 ขั้นตอนการปฏิบัติ

### ขั้นตอนที่ 1: ตรวจสอบ Charity Form

ก่อนเริ่ม Flow ตรวจสอบว่าคุณมีฟอร์ม "Charity Form" แล้วหรือไม่:

1. เข้าสู่ **Microsoft Forms** (https://forms.office.com)
2. ตรวจสอบว่ามีฟอร์มชื่อ `Charity Form` ที่มีคำถาม:
   - **What's your name?** (Text)
   - **What's your E-Mail address?** (Text)
   - **What amount do you plan on donating?** (Number)
   - **What type of donor describes you the best?** (Choice)
     - Options: `First time donor`, `Occasional donor`, `All-the-time donor`

> 💡 **หมายเหตุ**: ถ้ายังไม่มีฟอร์ม ให้สร้างตาม Exercise 3b หรือสร้างใหม่ตามขั้นตอนที่ 1 ของ Exercise 3b

### ขั้นตอนที่ 2: ตรวจสอบ Child Flow

ก่อนเริ่ม Parent Flow ตรวจสอบว่า Child Flow (Exercise 6a) พร้อมแล้ว:

1. เข้าสู่ **Power Automate** (https://make.powerautomate.com/)
2. เปิด Child Flow: `Exercise 6a - Child Flow Process Donor Type`
3. ตรวจสอบว่า Flow ถูก Save แล้ว
4. Copy **HTTP POST URL** จาก Trigger:
   - คลิกที่ **When an HTTP request is received**
   - Copy URL ที่แสดงใน Trigger (เช่น `https://prod-xxx.azure-apim.net/triggers/manual/...`)

> 💡 **หมายเหตุ**: URL นี้จะใช้ใน Parent Flow เพื่อเรียกใช้ Child Flow

### ขั้นตอนที่ 3: สร้าง Parent Flow ใหม่พร้อม Forms Trigger

1. เข้าสู่ **Power Automate** (https://make.powerautomate.com/)
2. คลิก **Create** → เลือก **Automated cloud flow**
3. ตั้งชื่อ Flow: `Exercise 6b - Parent Flow Call Child Flow`
4. ค้นหาและเลือก Trigger: **When a new response is submitted**
5. คลิก **Create**

### ขั้นตอนที่ 4: ตั้งค่า Forms Trigger

1. ใน **When a new response is submitted**:
   - **Form Id**: เลือก `Charity Form` (หรือชื่อฟอร์มที่คุณสร้าง)
   - **Connection**: ตรวจสอบว่าเชื่อมต่อกับ Microsoft Forms Account ของคุณแล้ว

> 💡 **หมายเหตุ**: Trigger นี้จะทำงานอัตโนมัติเมื่อมีคนส่งฟอร์ม "Charity Form"

### ขั้นตอนที่ 5: เพิ่ม Get response details

1. เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **Get response details**
3. ตั้งค่า:
   - **Form Id**: เลือก `Charity Form` (เหมือน Trigger)
   - **Response Id**: เลือก `Response Id` จาก Dynamic Content (จาก Trigger)

> 💡 **คำอธิบาย**: Get response details จะดึงข้อมูลรายละเอียดทั้งหมดจากฟอร์มที่ส่งมา
> - ข้อมูลที่ได้: ชื่อ, Email, จำนวนเงินบริจาค, ประเภทผู้บริจาค

**ข้อมูลที่สามารถเข้าถึงได้**:
- `What's your name?` → ชื่อผู้บริจาค
- `What's your E-Mail address?` → Email ผู้บริจาค
- `What amount do you plan on donating?` → จำนวนเงินบริจาค (Number)
- `What type of donor describes you the best?` → ประเภทผู้บริจาค

### ขั้นตอนที่ 6: ส่งข้อความแจ้งเตือนไปยัง Teams

1. เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **Post message in a chat or channel**
3. ตั้งค่า:
   - **Post as**: `Flow bot`
   - **Post in**: `Chat with Flow bot`
   - **Recipient**: ใส่ Email address ของคุณ (เช่น `your-email@example.com`)
   - **Message**: ใช้ Dynamic Content เพื่อสร้างข้อความ:
     ```
     {What's your name?} has submitted a Charity form.
     Processing donor type: {What type of donor describes you the best?}
     ```

> 💡 **คำอธิบาย**: Action นี้จะส่งข้อความแจ้งเตือนไปยัง Teams ทุกครั้งที่มีการส่งฟอร์ม

### ขั้นตอนที่ 7: เรียกใช้ Child Flow ด้วย HTTP Request

1. เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **HTTP** (หรือ **HTTP - HTTP**)
3. เลือก Action: **HTTP - HTTP**
4. ตั้งค่า:
   - **Method**: `POST`
   - **URI**: วาง HTTP POST URL ที่ Copy จาก Child Flow (จากขั้นตอนที่ 2)
   - **Headers**: เพิ่ม Header:
     - **Key**: `Content-Type`
     - **Value**: `application/json`
   - **Body**: คลิก **Expression** (fx) และพิมพ์:
     ```json
     {
       "donorType": "@{body('Get_response_details')?['r_3b3c4d5e6f7g8h9i0j1k2l3m4n5o6p']}",
       "name": "@{body('Get_response_details')?['r_1a2b3c4d5e6f7g8h9i0j1k2l3m4n5o6p']}",
       "email": "@{body('Get_response_details')?['r_2a3b4c5d6e7f8g9h0i1j2k3l4m5n6o7p8q']}",
       "amount": @{body('Get_response_details')?['r_4c5d6e7f8g9h0i1j2k3l4m5n6o7p8q9r']}
     }
     ```

> ⚠️ **คำเตือน**: Field Names ใน JSON Body อาจจะแตกต่างกันตามฟอร์มของคุณ
> - ใช้ Dynamic Content จาก Get response details แทน Field Names ที่แน่นอน
> - ตรวจสอบ Field Names ใน Dynamic Content

**วิธีที่ง่ายกว่า - ใช้ Dynamic Content**:
1. คลิกที่ **Body** field
2. คลิก **Expression** (fx) หรือใช้ **{}** เพื่อเปิด Dynamic Content
3. ใช้ Dynamic Content จาก Get response details:
   ```json
   {
     "donorType": "{What type of donor describes you the best?}",
     "name": "{What's your name?}",
     "email": "{What's your E-Mail address?}",
     "amount": {What amount do you plan on donating?}
   }
   ```

> 💡 **คำอธิบาย**: HTTP Request Action จะเรียกใช้ Child Flow โดยส่ง Input Parameters:
> - Method: POST
> - URI: HTTP POST URL ของ Child Flow
> - Body: JSON Object ที่มี donorType, name, email, amount

**ตัวอย่าง Request Body**:
```json
{
  "donorType": "First time donor",
  "name": "สมชาย ใจดี",
  "email": "somchai@example.com",
  "amount": 5000
}
```

### ขั้นตอนที่ 8: จัดการ Response จาก Child Flow

1. เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **Parse JSON** (หรือ **Data Operations** → **Parse JSON**)
3. ตั้งค่า:
   - **Content**: เลือก `Body` จาก Dynamic Content (จาก HTTP Action)
   - **Schema**: คลิก **Use sample payload to generate schema**
   - วาง JSON Sample:
     ```json
     {
       "success": true,
       "message": "Donor processed successfully",
       "donorType": "First time donor",
       "action": "Email sent to admin"
     }
     ```
   - คลิก **Done**

> 💡 **คำอธิบาย**: Parse JSON จะแปลง Response จาก Child Flow เป็น JSON Object
> - หลังจาก Parse JSON แล้ว จะสามารถเข้าถึง Fields ได้: success, message, donorType, action

### ขั้นตอนที่ 9: เพิ่ม Condition เพื่อตรวจสอบผลลัพธ์

1. เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **Condition** (หรือ **Control** → **Condition**)
3. ตั้งค่า:
   - **Choose a value**: เลือก `success` จาก Dynamic Content (จาก Parse JSON)
   - **Condition**: `is equal to`
   - **Choose a value**: พิมพ์ `true`

> 💡 **คำอธิบาย**: Condition นี้จะตรวจสอบว่า Child Flow ทำงานสำเร็จหรือไม่

### ขั้นตอนที่ 10: เพิ่ม Action ใน True Branch (Success)

1. **ภายใน True Branch** (If yes) คลิก **Add an action**
2. ค้นหาและเลือก **Post message in a chat or channel**
3. ตั้งค่า:
   - **Post as**: `Flow bot`
   - **Post in**: `Chat with Flow bot`
   - **Recipient**: ใส่ Email address ของคุณ (เช่น `your-email@example.com`)
   - **Message**: ใช้ Dynamic Content เพื่อสร้างข้อความ:
     ```
     ✅ Child Flow executed successfully!
     
     Donor Type: {donorType}
     Action: {action}
     Message: {message}
     ```

> 💡 **คำอธิบาย**: Action นี้จะส่งข้อความแจ้งเตือนเมื่อ Child Flow ทำงานสำเร็จ

### ขั้นตอนที่ 11: เพิ่ม Action ใน False Branch (Error)

1. **ภายใน False Branch** (If no) คลิก **Add an action**
2. ค้นหาและเลือก **Post message in a chat or channel**
3. ตั้งค่า:
   - **Post as**: `Flow bot`
   - **Post in**: `Chat with Flow bot`
   - **Recipient**: ใส่ Email address ของคุณ (เช่น `your-email@example.com`)
   - **Message**: ใช้ Dynamic Content เพื่อสร้างข้อความ:
     ```
     ❌ Child Flow execution failed!
     
     Message: {message}
     ```

> 💡 **คำอธิบาย**: Action นี้จะส่งข้อความแจ้งเตือนเมื่อ Child Flow ทำงานไม่สำเร็จ

### ขั้นตอนที่ 12: ทดสอบ Flow

#### ทดสอบกรณีที่ 1: "First time donor"

1. คลิก **Save**
2. เปิดฟอร์ม "Charity Form" ใน Browser
3. กรอกข้อมูล:
   - **What's your name?**: `ทดสอบ 1`
   - **What's your E-Mail address?**: `test1@example.com`
   - **What amount do you plan on donating?**: `3000`
   - **What type of donor describes you the best?**: `First time donor`
4. ส่งฟอร์ม
5. รอให้ Flow รันอัตโนมัติ
6. ตรวจสอบผลลัพธ์:
   - ควรเห็น Teams Message: `ทดสอบ 1 has submitted a Charity form.`
   - ควรเห็น Teams Message: `✅ Child Flow executed successfully!`
   - ควรเห็น Email: `We have a first time donor` (ส่งไปยัง Email ของคุณ)
   - Email Body มีข้อมูล: Name, Email, Amount

#### ทดสอบกรณีที่ 2: "Occasional donor"

1. เปิดฟอร์ม "Charity Form" อีกครั้ง
2. กรอกข้อมูล:
   - **What's your name?**: `ทดสอบ 2`
   - **What's your E-Mail address?**: `test2@example.com`
   - **What amount do you plan on donating?**: `5000`
   - **What type of donor describes you the best?**: `Occasional donor`
3. ส่งฟอร์ม
4. รอให้ Flow รันอัตโนมัติ
5. ตรวจสอบผลลัพธ์:
   - ควรเห็น Teams Message: `ทดสอบ 2 has submitted a Charity form.`
   - ควรเห็น Teams Message: `✅ Child Flow executed successfully!`
   - ควรเห็น Email: `Thank you for donating` (ส่งไปยัง `test2@example.com`)
   - Email Body: `Thank you so much more donating - you are clearly an occasional donor`

#### ทดสอบกรณีที่ 3: "All-the-time donor"

1. เปิดฟอร์ม "Charity Form" อีกครั้ง
2. กรอกข้อมูล:
   - **What's your name?**: `ทดสอบ 3`
   - **What's your E-Mail address?**: `test3@example.com`
   - **What amount do you plan on donating?**: `10000`
   - **What type of donor describes you the best?**: `All-the-time donor`
3. ส่งฟอร์ม
4. รอให้ Flow รันอัตโนมัติ
5. ตรวจสอบผลลัพธ์:
   - ควรเห็น Teams Message: `ทดสอบ 3 has submitted a Charity form.`
   - ควรเห็น Teams Message: `✅ Child Flow executed successfully!`
   - ควรเห็น Calendar Event: `We have all-time-donor! Let Discuss how to bring him /her in` (พรุ่งนี้)
   - Event มี Required attendees

---

## 📊 ผลลัพธ์ที่คาดหวัง

### Parent Flow Structure

```
When a new response is submitted (Charity Form)
  ↓
Get response details
  ↓
Post message (Teams) - แจ้งเตือนการส่งฟอร์ม
  ↓
HTTP Request - เรียกใช้ Child Flow
  ↓
Parse JSON - แปลง Response จาก Child Flow
  ↓
Condition: success = true?
  ├─ True: Post message (Teams) - Success
  └─ False: Post message (Teams) - Error
```

### กรณีที่ 1: "First time donor"

คุณควรจะเห็น:
- ✅ **Teams Message 1**: `{ชื่อ} has submitted a Charity form.`
- ✅ **Teams Message 2**: `✅ Child Flow executed successfully!`
- ✅ **Email**: `We have a first time donor` (ส่งไปยัง Email ของคุณ)
- ✅ **Email Body**: รวมข้อมูล Name, Email, Amount

### กรณีที่ 2: "Occasional donor"

คุณควรจะเห็น:
- ✅ **Teams Message 1**: `{ชื่อ} has submitted a Charity form.`
- ✅ **Teams Message 2**: `✅ Child Flow executed successfully!`
- ✅ **Email**: `Thank you for donating` (ส่งไปยัง Email จากฟอร์ม)
- ✅ **Email Body**: `Thank you so much more donating - you are clearly an occasional donor`

### กรณีที่ 3: "All-the-time donor"

คุณควรจะเห็น:
- ✅ **Teams Message 1**: `{ชื่อ} has submitted a Charity form.`
- ✅ **Teams Message 2**: `✅ Child Flow executed successfully!`
- ✅ **Calendar Event**: `We have all-time-donor! Let Discuss how to bring him /her in` (พรุ่งนี้)
- ✅ **Event มี Required attendees**

---

## 🎓 สิ่งที่ได้เรียนรู้

✅ **Parent Flow Concept**: สร้าง Flow ที่เรียกใช้ Child Flow  
✅ **Microsoft Forms Trigger**: ใช้ "When a new response is submitted" เพื่อ Trigger Flow อัตโนมัติ  
✅ **Get response details**: ดึงข้อมูลรายละเอียดทั้งหมดจากฟอร์มที่ส่งมา  
✅ **HTTP Request Action**: ใช้ HTTP Request เพื่อเรียกใช้ Child Flow  
✅ **Request Body**: ส่ง Input Parameters ไปยัง Child Flow  
✅ **Parse JSON**: แปลง Response จาก Child Flow เป็น JSON Object  
✅ **Condition**: ตรวจสอบผลลัพธ์จาก Child Flow  
✅ **Error Handling**: จัดการ Error เมื่อ Child Flow ทำงานไม่สำเร็จ  
✅ **Teams Integration**: ส่งข้อความไปยัง Teams Chat/Channel  
✅ **Dynamic Content**: ใช้ Dynamic Content จาก Forms และ HTTP Response ใน Actions  

---

## 💡 Tips & Tricks

1. **HTTP Request Action**:
   - ใช้ Method: POST สำหรับการเรียกใช้ Child Flow
   - URI: HTTP POST URL จาก Child Flow
   - Headers: เพิ่ม `Content-Type: application/json`
   - Body: JSON Object ที่มี Input Parameters

2. **Dynamic Content ใน Request Body**:
   - ใช้ Dynamic Content จาก Get response details
   - ตรวจสอบ Field Names ให้ถูกต้อง
   - ใช้ Expression (fx) ถ้าต้องการ Format ข้อมูล

3. **Parse JSON**:
   - ใช้ Parse JSON เพื่อแปลง Response จาก Child Flow
   - ใช้ Sample Payload เพื่อสร้าง Schema
   - ตรวจสอบ Schema ให้ตรงกับ Response จาก Child Flow

4. **Error Handling**:
   - ตรวจสอบ success field ใน Response
   - ส่งข้อความแจ้งเตือนเมื่อเกิด Error
   - ใช้ Try-Catch Pattern (ถ้าเป็นไปได้)

5. **Testing Parent Flow**:
   - ต้องส่งฟอร์มจริงๆ เพื่อ Trigger Flow
   - ตรวจสอบ Teams Messages และ Email
   - ตรวจสอบ Run History ใน Power Automate

6. **Child Flow URL**:
   - Copy HTTP POST URL จาก Child Flow Trigger
   - เก็บ URL ไว้ในที่ปลอดภัย
   - ตรวจสอบ URL ให้ถูกต้องก่อนใช้

---

## 🔄 แบบฝึกหัดเพิ่มเติม

1. **เพิ่ม Error Handling**:
   - เพิ่ม Try-Catch Pattern สำหรับ HTTP Request
   - จัดการ Error เมื่อ Child Flow ไม่สามารถเรียกใช้ได้
   - ส่งข้อความแจ้งเตือนเมื่อเกิด Error

2. **เพิ่ม Logging**:
   - บันทึก Log ทุกครั้งที่มีการเรียกใช้ Child Flow
   - ใช้ SharePoint List หรือ Storage Account เพื่อเก็บ Log
   - บันทึก Input Parameters และ Response

3. **เพิ่ม Retry Logic**:
   - เพิ่ม Retry เมื่อ Child Flow ทำงานไม่สำเร็จ
   - ใช้ Do Until Loop เพื่อ Retry
   - จำกัดจำนวนครั้งที่ Retry

4. **เพิ่ม Timeout Handling**:
   - ตั้งค่า Timeout สำหรับ HTTP Request
   - จัดการ Timeout เมื่อ Child Flow ทำงานนานเกินไป
   - ส่งข้อความแจ้งเตือนเมื่อ Timeout

5. **เรียกใช้ Child Flow หลายตัว**:
   - สร้าง Child Flow อื่นๆ (เช่น Validate Email, Calculate Discount)
   - เรียกใช้ Child Flow หลายตัวใน Parent Flow
   - จัดการ Response จาก Child Flow หลายตัว

6. **เพิ่ม Conditional Child Flow Calls**:
   - เรียกใช้ Child Flow เฉพาะเมื่อเงื่อนไขเป็นจริง
   - ใช้ Condition เพื่อตรวจสอบก่อนเรียกใช้ Child Flow
   - เรียกใช้ Child Flow ต่างกันตามเงื่อนไข

---

## ❓ คำถามที่พบบ่อย (FAQ)

**Q: Parent Flow คืออะไร?**  
A: Parent Flow คือ Flow ที่เรียกใช้ Child Flow เพื่อทำงานบางอย่าง

**Q: ทำไมต้องใช้ HTTP Request Action?**  
A: เพราะ Child Flow ใช้ HTTP Request Trigger, Parent Flow ต้องเรียกใช้ผ่าน HTTP Request

**Q: วิธี Copy HTTP POST URL จาก Child Flow?**  
A: เปิด Child Flow → คลิกที่ HTTP Request Trigger → Copy URL ที่แสดงใน Trigger

**Q: Parse JSON จำเป็นหรือไม่?**  
A: จำเป็น, เพื่อแปลง Response จาก Child Flow เป็น JSON Object ที่สามารถเข้าถึง Fields ได้

**Q: วิธีทดสอบ Parent Flow?**  
A: ต้องส่งฟอร์มจริงๆ เพื่อ Trigger Flow, หรือใช้ Test mode ใน Power Automate

**Q: สามารถเรียกใช้ Child Flow หลายตัวได้ไหม?**  
A: ได้, สามารถเรียกใช้ Child Flow หลายตัวใน Parent Flow ได้

**Q: วิธีจัดการ Error เมื่อ Child Flow ทำงานไม่สำเร็จ?**  
A: ตรวจสอบ success field ใน Response และใช้ Condition เพื่อจัดการ Error

**Q: Request Body ต้องเป็น JSON หรือไม่?**  
A: ใช่, Request Body ต้องเป็น JSON Object ที่ตรงกับ Schema ของ Child Flow

**Q: วิธีตรวจสอบ Field Names ใน Get response details?**  
A: ใช้ Dynamic Content เพื่อดู Field Names ที่มีอยู่, หรือใช้ Expression เพื่อเข้าถึง Field

---

## 📝 สรุป

ใน Exercise นี้ คุณได้เรียนรู้:
- การสร้าง Parent Flow ที่เรียกใช้ Child Flow
- การใช้ Microsoft Forms Trigger และ Get response details
- การใช้ HTTP Request Action เพื่อเรียกใช้ Child Flow
- การส่ง Input Parameters ไปยัง Child Flow
- การรับ Response จาก Child Flow และจัดการผลลัพธ์
- การใช้ Parse JSON เพื่อแปลง Response
- การใช้ Condition เพื่อตรวจสอบผลลัพธ์
- การจัดการ Error เมื่อ Child Flow ทำงานไม่สำเร็จ

**Parent Flow Structure**:
```
When a new response is submitted (Charity Form)
  ↓
Get response details
  ↓
Post message (Teams) - แจ้งเตือนการส่งฟอร์ม
  ↓
HTTP Request - เรียกใช้ Child Flow
  Body: {
    "donorType": "...",
    "name": "...",
    "email": "...",
    "amount": ...
  }
  ↓
Parse JSON - แปลง Response จาก Child Flow
  ↓
Condition: success = true?
  ├─ True: Post message (Teams) - Success
  └─ False: Post message (Teams) - Error
```

**Parent-Child Flow Communication**:
```
Parent Flow:
  HTTP Request → Child Flow
  Body: Input Parameters
  ↓
Child Flow:
  Process Input → Execute Logic
  ↓
Parent Flow:
  Receive Response → Parse JSON → Handle Result
```

**ข้อดีของการใช้ Parent-Child Flow**:
- ✅ Code Reusability (นำ Child Flow กลับมาใช้ซ้ำได้)
- ✅ Maintenance Efficiency (ง่ายต่อการบำรุงรักษา)
- ✅ Consistency (ความสอดคล้อง)
- ✅ Modularity (แยกเป็นโมดูล)
- ✅ Separation of Concerns (แยกหน้าที่ชัดเจน)

**เมื่อไหร่ควรใช้ Parent-Child Flow**:
- ✅ Logic ที่ใช้ซ้ำในหลาย Flow
- ✅ Operations ที่ซับซ้อนและต้องการแยกออกมา
- ✅ กระบวนการมาตรฐานที่ใช้ทั่วทั้งองค์กร
- ✅ ฟังก์ชัน Utility ที่ใช้ร่วมกัน

พร้อมสำหรับบทต่อไปแล้ว! 🎉

